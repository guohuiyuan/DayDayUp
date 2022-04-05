<!--
 * @Description: 
 * @Author: Alone
 * @Date: 2022-04-05 20:02:22
 * @LastEditors: Alone
 * @LastEditTime: 2022-04-05 21:28:26
-->

# ThreadLocal

## 简介

ThreadLocal可以解释为线程的局部变量。适用于变量在线程间隔离而在方法或类间共享的场景。

## 实例

```
public class ThreadLocalExample {
    private static ThreadLocal<String> local = new ThreadLocal<String>();

    static void print(String str) {
        //打印当前线程中本地内存中变量的值
        System.out.println(str + " :" + local.get());
        //清除内存中的本地变量
        local.remove();
    }
    public static void main(String[] args) throws InterruptedException {

        new Thread(new Runnable() {
            public void run() {
                ThreadLocalExample.local.set("xdclass_A");
                print("A");
                //打印本地变量
                System.out.println("清除后：" + local.get());
            }
        },"A").start();
        Thread.sleep(1000);

        new Thread(new Runnable() {
            public void run() {
                ThreadLocalExample.local.set("xdclass_B");
                print("B");
                System.out.println("清除后 " + local.get());
            }
        },"B").start();
    }
}

```

结果为：

```
A :xdclass_A
清除后：null
B :xdclass_B
清除后 null
```

两个线程均获取到了自己线程存放的变量。

## 使用注意事项

- 在线程池中慎用ThreadLocal，因为线程池中对线程的管理都是采取线程复用的方法。所以在线程池中线程非常难结束，导致线程的持续时间不可估测。
- 在异步程序中慎用ThreadLocal，因为ThreadLocal的参数传递是不可靠的，因为线程将请求发送后，不会等待结果返回。
- 在使用完ThreadLocal后，需要调用`remove()` 方法，防止内存溢出

## 实现原理


ThreadLocal类图：

![](https://i0.hdslb.com/bfs/album/f648b2f4b02cdbb8d6ae75df4fe0472f7a64c8e8.png)

Thread类中存在ThreadLocalMap的引用：

```
/* ThreadLocal values pertaining to this thread. This map is maintained
 * by the ThreadLocal class. */
ThreadLocal.ThreadLocalMap threadLocals = null;

/*
 * InheritableThreadLocal values pertaining to this thread. This map is
 * maintained by the InheritableThreadLocal class.
 */
ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
```

### set方法

```
public void set(T value) {
    // 找到当前线程
    Thread t = Thread.currentThread();
    // 拿到当前线程的ThreadLocalMap
    ThreadLocalMap map = getMap(t);
    // 如果map不为空就直接插入，如果为空就创建
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}

private static final int INITIAL_CAPACITY = 16;

ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
    table = new Entry[INITIAL_CAPACITY];
    int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
    table[i] = new Entry(firstKey, firstValue);
    size = 1;
    setThreshold(INITIAL_CAPACITY);
}

static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

首先找到当前线程获取当前线程的ThreadLocalMap,如果有就直接设值，没有就创建一个新的容量为16的map。

### get方法

```
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}

private T setInitialValue() {
    T value = initialValue();
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
    return value;
}
```

获取到当前线程Thread,根据Thread获取到对应的ThreadLocalMap,从ThreadLocalMap中再获取当前ThreadLocal对应的Entry，并返回Entry中的值即可。

### remove方法

```
public void remove() {
    ThreadLocalMap m = getMap(Thread.currentThread());
    if (m != null)
        m.remove(this);
}


private void remove(ThreadLocal<?> key) {
    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len-1);
    for (Entry e = tab[i];
        e != null;
        e = tab[i = nextIndex(i, len)]) {
        if (e.get() == key) {
            e.clear();
            expungeStaleEntry(i);
            return;
        }
    }
}

private int expungeStaleEntry(int staleSlot) {
            Entry[] tab = table;
            int len = tab.length;

            // expunge entry at staleSlot
            tab[staleSlot].value = null;
            tab[staleSlot] = null;
            size--;

            // Rehash until we encounter null
            Entry e;
            int i;
            for (i = nextIndex(staleSlot, len);
                 (e = tab[i]) != null;
                 i = nextIndex(i, len)) {
                ThreadLocal<?> k = e.get();
                if (k == null) {
                    e.value = null;
                    tab[i] = null;
                    size--;
                } else {
                    int h = k.threadLocalHashCode & (len - 1);
                    if (h != i) {
                        tab[i] = null;

                        // Unlike Knuth 6.4 Algorithm R, we must scan until
                        // null because multiple entries could have been stale.
                        while (tab[h] != null)
                            h = nextIndex(h, len);
                        tab[h] = e;
                    }
                }
            }
            return i;
        }
```

## ThreadLocal产生脏数据

在线程池的线程复用中，由于线程池会重用Thread对象，那么与Thread类绑定的ThreadLocalMap也会被重用。如果不显示的清理ThreadLocalMap的值，或重新set值，就可能get到重用的线程信息。

## ThreadLocal的内存泄漏

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/25/1710f2c760e6d89e~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

ThreadLocalMap中的每个Entry都是一个对键的弱引用(WeakReference)，每个Entry都包含了一个对值的强引用。当JVM进行垃圾回收时，无论内存是否充足，都会回收只被弱引用关联的对象。即使线程正在执行中， 只要 ThreadLocal 对象引用被置成 null，Entry 的 Key 就会自动在下一次 YGC 时被垃圾回收（因为只剩下ThreadLocalMap 对其的弱引用，没有强引用了）。如果这里Entry 的key 值是对 ThreadLocal 对象的强引用的话，那么即使ThreadLocal的对象引用被声明成null 时，这些 ThreadLocal 不能被回收，因为还有来自 ThreadLocalMap 的强引用，这样子就会造成内存泄漏。这类 key == null 的Entry 会在下一次执行 ThreadLocalMap 的 getEntry 和 set 方法中，将这些 entry 的 value 置为 null，使得原来value 指向的变量可以被垃圾回收。

在ThreadLocal源码注释中声明了ThreadLocal对象通常作为私有静态变量使用，那么其生命周期至少不会随着线程结束而结束。也就是说，绝大多数的静态threadLocal对象都不会被置为null。这样子的话，通过 stale entry 这种机制来清除Value 对象实例这条路是走不通的。必须要手动remove() 才能保证。


# 参考目录

1. [讲透 ThreadLocal 和 InheritableThreadLocal](https://juejin.cn/post/6844904102015713293)
2. [ThreadLocal的介绍+经典应用场景](https://juejin.cn/post/7042211997743579144)
3. [多线程热知识（一）：ThreadLocal简介及底层原理](https://juejin.cn/post/7064166064816390157#heading-2)