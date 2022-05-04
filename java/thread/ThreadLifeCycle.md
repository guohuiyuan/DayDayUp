<!--
 * @Description: 
 * @Author: Alone
 * @Date: 2022-05-04 10:13:37
 * @LastEditors: Alone
 * @LastEditTime: 2022-05-04 16:05:32
-->
# 线程生命周期

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/1/1730adda067993c5~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

在java中，给定时间点上一个线程只能处于一种状态。以下状态均为JVM内的线程状态。

新建状态(New):使用`new` 创建一个线程对象，仅仅在堆中分配内存空间，在调用start方法之前的线程所处的状态，在此状态下，只是创建了一个线程对象存储在堆中。

可运行状态(runnable)：

当新建状态下的线程对象调用了start方法，该线程对象就从新建状态进入可运行状态（runnable）；线程对象的start方法只能调用一次，多次调用会发生`IllegalThreadStateException` 

可运行状态又可以细分成两种状态，ready（就绪状态）和running（运行状态）。

就绪状态是指线程对象调用start方法之后，等待JVM的调度(此时该线程并没有运行)，还未开始运行。

运行状态是指线程对象已获得JVM调度，处在运行中。

阻塞状态是指处于运行中的线程因为某些原因放弃CPU时间片，暂时停止运行。阻塞状态只能先进入就绪状态，进而由操作系统转到运行状态，不能直接进入运行状态；阻塞状态在锁被占用和发出IO请求时会发生。

等待状态是指运行中的线程调用了无参数的wait方法，然后JVM会把该线程储存到共享资源的对象等待池中，该线程进入等待状态；处于该状态中的线程只能被其他线程唤醒。

计时等待状态是指运行中的线程调用了带参数的wait方法或者sleep方法，此状态下的线程不会释放同步锁/同步监听器。

终止状态是表示线程终止，不可重启启动。线程正常执行完run方法和遇到异常退出都进入终止状态。

# 联合线程

线程使用join方法时表示一个线程等待另一个线程完成后才执行，join方法被调用后，调用join方法的线程对象所在线程处于阻塞状态，调用join的线程对象进入运行状态。

```java
public class JoinThreadDemo {
    public static void main(String []args) throws Exception {
       	System.out.println("开始");
		JoinThread join = new JoinThread();
		for (int i = 0; i < 50; i++) {
			System.out.println("i : " + i);
			if ( i == 10) {
				join.start();
			}
			if (i == 20) {
				join.join();
			}
		}
		System.out.println("结束");
    }
}

class JoinThread extends Thread {
	@Override
	public void run() {
		for (int i = 0; i < 50; i++) {
			System.out.println("join : " + i);
		}
	}
}

```

运行结果：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/1/1730adda13fc888d~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

# 线程礼让

yield方法：表示当前线程对象提示调度器自己愿意让出CPU时间片，但是调度器却不一定会采纳。

sleep方法和yield方法的区别：

1. 共同点是都能使当前处于运行状态的线程放弃CPU时间片，把运行的机会给其他线程
2. 不同点在于：sleep方法会给其他线程运行机会，不区分优先级；而yield方法只会给相同优先级或者更高优先级的线程运行的机会；
3. 调用sleep方法 后，线程进入计时等待状态，而调用yield方法后，线程进入就绪状态

# 参考资料

1. [「JAVA」线程生命周期分阶段详解，哲学家们深感死锁难解](https://juejin.cn/post/6845166890399055886)