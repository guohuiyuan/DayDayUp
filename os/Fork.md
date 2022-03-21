# fork()函数

```c
#include <unistd.h>

// 参数	： void 
// 返回值： pid_t 创建的子进程ID
pid_t fork(void);
```
返回值：`fork()` 返回值会返回两次，分别在父进程和子进程中返回。

1. 在父进程中返回子进程的ID，在子进程中返回0。所以可以通过`fork`的返回值来区分父进程与子进程。
1. 在父进程中返回 -1 ，表示创建子进程失败，并设置`errorno`。如下面两种情况导致创建失败：
- 当前系统的进程数已经达到了系统规定的上限，这时`errno`的值被设置为`EAGAIN`
- 系统内存不足，这时 errno 的值被设置为`ENOMEM`

# 程序实例

```c
 #include <unistd.h>
 #include <stdio.h>
 
 int main() {
     pid_t cld_pid;
     int a = 1, b = 2;
     for (int i = 0; i < 2; i++) {
         if ((cld_pid = fork()) == 0) {
             a += 1;
             printf("a=%d  b=%d\n", a, b);
         } else {
             b += 1;
             printf("a=%d  b=%d\n", a, b);
         }
     }
     return 0;
 }
```

执行过程：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e8a76688828478b9d0af8d8679a2960~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

# 原理

Linux的`fork`是通过**写时拷贝(Copy On Write)**实现的。在执行fork语句后，内核不是复制一份父进程的整个地址空间，而是**父子进程共享父进程的地址空间**。在父进程或子进程进行写操作时，子进程才复制一份地址空间，从而使得父子进程拥有自己的虚拟地址空间，在自己的地址空间进行写操作。
**对于文件资源，fork之后的父子进程共享文件，fork之后的父进程与子进程的文件描述符表指向相同的文件表，引用计数增加，共享文件偏移指针。**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2f5f60cfd1c145309b384c90ed9d0249~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

# fork引发的死锁问题

**fork函数在创建子进程时，如果原进程为多线程进程，则只会复制当前线程，不会复制其他线程。**

现在有这样一个问题：
该程序有个全局对象sGlobalInstance，父进程先通过该对象执行了lock操作，然后执行fork，在子进程中，也去执行lock操作。

```c
#include <errno.h>
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/syscall.h>
#include <unistd.h>

class Test {
public:
    Test() {
        pthread_mutex_init(&mMutex, nullptr);
        printf("Init test instance pid:%u tid:%u\n", getpid(), gettid());
    }

    ~Test() {
        pthread_mutex_destroy(&mMutex);
    }

    void lock() {
        pthread_mutex_lock(&mMutex);
    }

    void unlock() {
        pthread_mutex_unlock(&mMutex);
    }

private:
    pthread_mutex_t mMutex;
};

static Test* sGlobalInstance = nullptr;

void* func(void* arg) {
    if (sGlobalInstance == nullptr) {
        sGlobalInstance = new Test();
    }

    printf("Before get lock pid:%u tid:%u\n", getpid(), gettid());
    sGlobalInstance->lock();
    printf("After get lock pid:%u tid:%u\n", getpid(), gettid());

    pause();
    return nullptr;
}

int main() {
    printf("In parent process. pid:%u tid:%u\n", getpid(), gettid());
    sGlobalInstance = new Test();

    pthread_t id;
    pthread_create(&id, nullptr, func, nullptr);
    // Sleep to make sure the thread get lock
    sleep(1);

    int pid = fork();
    if (pid < 0) {
        printf("Error occur while fork. errno:%d\n", errno);
        return errno;
    } else if (pid == 0) {
        // In child process
        printf("In child process. pid:%u tid:%u\n", getpid(), gettid());
        func(nullptr);
    } else {
        // In parent process
        pause();
    }
    
    return 0;
}
```

上面程序的执行结果如下：**子进程没有拿到锁，产生了死锁。**

```c
In parent process. pid:22287 tid:22287
Init test instance pid:22287 tid:22287
Before get lock pid:22287 tid:22288
After get lock pid:22287 tid:22288
In child process. pid:22293 tid:22293
Before get lock pid:22293 tid:22293
```

从执行流程看，该全局变量只在父进程中被初始化了一次，此时已经加上锁了。由于fork的cow机制，此时子进程中该变量也是加锁了的，当父进程对该变量进行解锁时，操作系统会复制一份进程中的资源，导致子进程中该变量仍是加锁了的。

解决方式：

`pthread_atfork` 函数可以用来处理这种情况，该函数原型如下：

- 回调函数prepare在fork前调用
- fork后在父进程中调用回调函数parent
- fork后在子进程中调用回调函数child
```c
int pthread_atfork(void (*prepare)(void), void (*parent)(void), void (*child)(void));
```
# fork之后的父子进程执行顺序问题

**进程的执行顺序是要看操作系统如何进行进程调度的，具体看调度算法。**

如果fork之后先调度父进程，此时父进程在cpu中处于活跃状态，无需进行进程切换操作，能提高性能。

如果fork之后先调度子进程立即exec（进程切换？）的情况，此时所有的父进程页面会因为cow进入保护状态。由于父进程有倾向继续修改内存页和栈页，所以需要为子进程复制一份页面，此时有发生页中断的潜在危险，对于子进程来说，它未必需要父进程的页面。此时如果先执行子进程做exec，就可以省去很多拷贝页面的过程。
如果需要强制子进程先运行的话可以使用vfork。

# 参考资料

1. [Linux进程Fork详解](https://juejin.cn/post/7022470737617223717)
2. [Linux进程创建之fork浅析](https://juejin.cn/post/6912612368996368392)
3. [一次fork引发的惨案](https://juejin.cn/post/7076006639907635207)
4. [fork导致的死锁问题](https://juejin.cn/post/6996608467192512549)
5. [OS中关于父子进程的执行顺序和多个子进程之间的执行顺序（整理）](https://blog.csdn.net/deniece1/article/details/102983771)
6. [fork之后，父子进程的先后执行顺序如何反映？](https://www.zhihu.com/question/59296096?sort=created)
