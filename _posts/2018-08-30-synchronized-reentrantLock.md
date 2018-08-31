---
layout:       post
title:        "浅析Synchronized与ReentrantLock"
subtitle:     "分析Synchronized与ReentrantLock的区别以及锁相关的一些概念"
date:         2018-08-27 22:42:05
author:       "Hyuga"
header-img:   "img/2018-08-27/head-top-1.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - lock
---

这两天看List相关源码，发现Vector采用synchronized加锁，而CopyOnWriteArrayList采用ReentrantLock加锁。

这两者有什么区别？下面我们一点点对比看看。

## 相关概念
#### CAS(Compare and Swap)
CAS的全称是Compare And Swap 即比较交换，其算法核心思想如下

> 执行函数：CAS(V,E,N)

- V表示要更新的变量
- E表示预期值
- N表示新值

若V=E，则V=N

若V!=E，说明V已经被其他线程更新过，则当前线程放弃修改。

从JDK 1.5开始提供了java.util.concurrent.atomic包，包中有许多基于CAS实现的原子操作类
- AtomicBoolean 原子更新布尔类型
- AtomicInteger 原子更新整型
- AtomicLong 原子更新长整型

{% highlight java %}
AtomicBoolean isLock = new AtomicBoolean(false);
isLock.compareAndSet(false, doSomethings(...));
//doSomethings()方法中所有操作保证原子性。
{% endhighlight %}

#### 锁概念
- 公平锁

    多个线程等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁。

- 非公平锁

    多个线程等待同一个锁时，不按照申请锁的时间顺序获得锁，而是根据cpu调度随机分配。

- 可重入锁

    加锁代码块只允许一个线程获得对象锁，当线程取锁成功，则加锁对象上的加锁数量计数器+1，反之释放锁则-1，允许同个线程多次入锁，无需等待，所以叫可重入锁。

- 排它锁、互斥锁

    同一时间只允许一个线程获得对象锁，进行操作，其他线程阻塞在外等待锁释放。

- 自旋锁

    JVM让当前想要获得锁的线程进行空循环，若干次循环后，如果得到锁则进入临界区；如果还得不到则线程挂起。线程数越来越多时自旋会占用大量cpu资源。


#### 内存共享之原子性
> 原子性是指操作是不可分割的

线程是CPU调度的基本单位，cpu有时间片的概念，会根据不同的调度算法进行线程调度。

**解析**

假如：cpu将1s分割为10等份，当线程1消耗完自己那一等份的时间后，就暂时失去cpu的使用权，等待下一次争抢到cpu时间片再继续执行。
定义了类变量`private static int a = 0;`，在main中执行`a++;`，`a++;`编译后实际上是三个指令：
- 读取变量a的值
- a的值+1
- 将值赋予变量a

在多线程场景下，线程1执行了第1步读取变量a的值，然后耗尽了当前拥有的cpu时间片，暂时停止了执行。
这时线程2争抢到了cpu时间片，执行完了第3步后耗尽时间片，这时a的值变为1.
接着线程1又抢到了时间片执行第2步，预期结果是1，实际结果为2.
这不是我们期望的结果，Java中的锁机制解决了原子性的问题。

**代码怎么实现原子性？**
- synchronized修饰方法：只允许一个获得锁的线程对方法操作。
- synchronized修饰代码块：只允许一个获得锁的线程对代码块操作。

#### 内存共享之可见性
> 可见性是指一个线程对共享变量的修改，另一个线程中同一个变量值会更新。

**解析**

java线程通信是通过共享内存的方式进行，而cpu的性能远高于内存性能，同样的时间cpu可以比内存读写做更多的事，所以计算机中使用了高速缓存作为cpu和内存之间的衔接。cpu操作高速缓存，高速缓存在适当的时候再和主内存同步。

在多线程场景下，每个线程从高速缓存中拷贝的变量是互不可见的，这将会造成线程中高速缓存与主内存数据不同步，造成数据错误。

假如：主内存有`共享变量`i=1，线程1从高速缓存中拿到的i为1，线程2也从高速缓存中拿到i为1，线程2将i=2更新会主内存了，但是线程1还是继续对i=1进行操作。

内存模型JMM规定了所有`共享变量`都存在主内存，每条线程有自己的工作内存，工作内存中存放了该线程所用到的存在于主内存的`共享变量`副本，线程内的所有操作都是操作该副本，而不是直接操作主内存。
不同线程间的工作内存互不可见，变量传递都通过自身的工作内存和主内存进行同步。

**代码怎么实现可见性？**
- 使用volatile关键字：当jvm操作完volatile修饰的共享变量后，会先更新到主内存，并通知其他拷贝了该共享变量的线程，将工作内存中的变量副本置无效，并重新重主内存中拷贝新的变量副本。
- 使用锁机制：比如使用synchronized或者reentrantLock等将方法或者代码块将整个共享变量的操作包起来，保证原子性，同时也保证了可见性。

#### 内存共享之有序性
> 有序性是指程序的代码执行顺序和语句的顺序是一致的。

**解析**

上面的意思就是说可能会出现不一致的情况咯？原因是由于JVM的指令重排序优化造成的。

java允许编译器和处理器对源码编译生成的字节码指令进行重排序，优化性能，但是重排序的前提是：指令重排后的执行结果和重排前的执行结果，在单线程下必须一致。

重排序简单点理解就是两行代码顺序是1、2，编译过程中有可能在不影响单线程执行结果的前提下，为了提升指令执行的效率和性能，编译器生成的字节码指令顺序变成了2、1，单线程下执行结果不受影响。

**指令重排的优缺点**
- 优点
    - 指令重排能提升性能，比如：锁粒度优化，锁丢弃，无效引用丢弃等。
    - 单线程，或者多线程采用synchronized修饰方法，编译后方法中的字节码指令每次都是单线程操作，指令重排后执行效率更高，性能更好。
- 缺点
    - 指令重排后会影响到多线程并发执行的正确性
    - 多线程下，指令重拍后，如果没通过锁机制或者`volatile`关键字为字节码添加内存屏障指令，可能会造成并发错误。

**什么是内存屏障？**
> 内存屏障：禁止`编译器`和`处理器`对指定范围内的字节码指令进行重排序。保证java内存模型的有序性。

可见性分类：
- 加载屏障（Load Barrier）：刷新处理器缓存。
- 存储屏障（Store Barrier）：冲刷处理器缓存。

> 1. JVM在monitorenter（申请锁）对应的机器码指令后面，临界区代码开始之前插入Load Barrier，以达到临界区内部使用的共享变量都是新值的作用。
> 2. JVM在monitorexit（释放锁）对应的指令后插入Store Barrier，以达到临界区对共享变量的更改及时写回主存。

**有序性分类：**
- 获取屏障（Acquire Barrier）：在一个读操作之后插入一个屏障，禁止与之后的任何读写操作重排。
- 释放屏障（Release Barrier）：在一个写操作之前插入一个屏障，禁止与其前面任何读写操作重排。

> JVM会在monitorenter对应的机器码指令后面，临界区代码开始之前插入Acquire Barrier，并在临界区之后monitorexit之前插入Release Barrier。

## synchronized
> `synchronized` 是Java提供的一个并发控制的关键字。用于方法加锁和代码块加锁以实现同步，防止线程并发。

- 非公平锁
- 可重入锁

**源码**
{% highlight java %}
public class Test {
    private static final Object LOCK = new Object();

    public static void main(String[] args) {
        synchronized (LOCK) {
            System.out.println("lock block");
        }
    }
}
{% endhighlight %}
**javap -c Test**
{% highlight java %}
public class hyuga.excel.Test {
  public static void main(java.lang.String[]);
    Code:
       0: getstatic     #2                  // Field LOCK:Ljava/lang/Object;
       3: dup
       4: astore_1
       5: monitorenter
       6: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
       9: ldc           #4                  // String lock block
      11: invokevirtual #5                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      14: aload_1
      15: monitorexit
      16: goto          24
      19: astore_2
      20: aload_1
      21: monitorexit
      22: aload_2
      23: athrow
      24: return
    Exception table:
       from    to  target type
           6    16    19   any
          19    22    19   any
}
{% endhighlight %}

编译synchronized修饰的代码，可以看到同步块的代码前后添加了`monitorenter`、`monitorexit`这两个字节码指令。
jvm执行到`monitorenter`的时候，会尝试获取对象锁，如果获取不到，则放弃当前拥有的时间片，等下一个时间片再尝试获取锁；如果获取到锁，则执行包含的代码块，执行到`monitorexit`的时候，则释放锁。

每次执行`monitorenter`进入代码块，则加锁对象上的锁计数器+1，每次执行`monitorexit`则加锁对象上的所计数器-1.当计数器为0则表示锁已被释放。

**`synchronized`在JVM中的体现**
- `同步方法`：JVM采用`ACC_SYNCHRONIZED`标记符来实现同步。

- `同步代码块`：JVM采用`monitorenter`、`monitorexit`两个指令来实现同步。

> synchronized 保证了原子性、可见性、有序性

## ReentrantLock
java.util.concurrent包下提供的一个互斥锁、可重入锁，是jdk提供给用户对代码进行显式加锁，而不是像synchronized一样作为语言特性来实现。

**实现原理：**
>
> ReentrantLock是一种自旋锁，通过循环调用CAS操作来实现加锁。它的性能比较好也是因为避免了使线程进入内核态的阻塞状态。想尽办法避免线程进入内核的阻塞状态是我们去分析和理解锁设计的关键钥匙。

- 比synchronized功能更强大，可定制
    - 避免死锁，等待可中断。
        - 持锁线程长期不释放锁，正在等待的线程可以选择放弃等待
    - 默认非公平锁，但可设置为公平锁
        - Lock lock = new ReentrantLock(boolean flag); 可以设置true创建公平锁
    - 绑定多个Condition
        - 通过多次newCondition可以获得多个Condition对象,可以简单实现比较复杂的线程同步功能
    - 可重入锁，同synchronized

**简单用法**
{% highlight java %}
Lock lock = new ReentrantLock();
lock.lock();
...
lock.unlock();
{% endhighlight %}

**构造函数**
{% highlight java %}
//无参构造器（默认非公平锁）
public ReentrantLock() {
    sync = new NonfairSync();
}

//带布尔值的构造器
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();//fair为true，公平锁；反之，非公平锁
}
{% endhighlight %}

**常用方法**
- lock( ): 加锁
- lockInterruptibly( ): 加锁（支持响应终端的锁，当线程中断会自动释放锁）
- tryLock( ): 尝试获取锁，成功立即返回true，失败立即返回false，不阻塞
- unlock( ): 释放锁
- newCondition( ): 创建一个加锁条件，ReentrantLock支持多个Condition，也可以理解为标记
    - await( ): 将当前线程打上标记，并进入等待池
    - signal( ): 随机唤醒对应标记的某一条线程
    - signalAll( ): 唤醒对应标记的所有线程

下面举个例子说明中断响应
{% highlight java %}
private final static ReentrantLock LOCK1 = new ReentrantLock();
private final static ReentrantLock LOCK2 = new ReentrantLock();
private int lock;

@Override
public void run() {
    try {
        if (lock == 1) {
            System.out.println(Thread.currentThread().getName() + "给LOCK1加锁开始");
            LOCK1.lockInterruptibly();
            System.out.println(Thread.currentThread().getName() + "给LOCK1加锁成功");
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
            }
            System.out.println(Thread.currentThread().getName() + "给LOCK2加锁开始");
            LOCK2.lockInterruptibly();
            System.out.println(Thread.currentThread().getName() + "给LOCK2加锁成功");
            System.out.println(Thread.currentThread().getName() + ":拿到两个lock，并完成了工作");
        } else {
            System.out.println(Thread.currentThread().getName() + "给LOCK2加锁开始");
            LOCK2.lockInterruptibly();
            System.out.println(Thread.currentThread().getName() + "给LOCK2加锁成功");
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
            }
            System.out.println(Thread.currentThread().getName() + "给LOCK1加锁开始");
            LOCK1.lockInterruptibly();
            System.out.println(Thread.currentThread().getName() + "给LOCK1加锁成功");
            System.out.println(Thread.currentThread().getName() + ":拿到两个lock，并完成了工作");
        }
    } catch (InterruptedException e) {
        e.printStackTrace();
    } finally {
        if (LOCK1.isHeldByCurrentThread()) {
            System.out.println(Thread.currentThread().getName() + "给LOCK1释放锁开始");
            LOCK1.unlock();
            System.out.println(Thread.currentThread().getName() + "给LOCK1释放锁成功");
        }
        if (LOCK2.isHeldByCurrentThread()) {
            System.out.println(Thread.currentThread().getName() + "给LOCK2释放锁开始");
            LOCK2.unlock();
            System.out.println(Thread.currentThread().getName() + "给LOCK2释放锁成功");
        }
        System.out.println(Thread.currentThread().getName() + ":线程退出");
    }
}

public static void main(String arg[]) throws InterruptedException {
    Thread t1 = new Thread(new Test(1));
    Thread t2 = new Thread(new Test(2));
    t1.setName("线程1");
    t2.setName("线程2");
    t2.start();
    t1.start();
    Thread.sleep(1000);
    System.out.println(Thread.currentThread().getName() + "中断线程2响应开始");
    t2.interrupt();
    System.out.println(Thread.currentThread().getName() + "中断线程2响应成功");
}
{% endhighlight %}

输出结果
```
线程2给LOCK2加锁开始
线程1给LOCK1加锁开始
线程1给LOCK1加锁成功
线程2给LOCK2加锁成功
线程1给LOCK2加锁开始（此处开始死锁，线程1、2分别给lock1和lock2加锁成功，彼此再去加锁lock1和lock2，会陷入死循环）
线程2给LOCK1加锁开始
java.lang.InterruptedException
at java.util.concurrent.locks.AbstractQueuedSynchronizer.doAcquireInterruptibly(AbstractQueuedSynchronizer.java:898)
at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireInterruptibly(AbstractQueuedSynchronizer.java:1222)
at java.util.concurrent.locks.ReentrantLock.lockInterruptibly(ReentrantLock.java:335)
at hyuga.excel.Test.run(Test.java:45)
at java.lang.Thread.run(Thread.java:748)
main中断线程2响应开始`（主线程中断了线程2）`
main中断线程2响应成功
线程2给LOCK2释放锁开始`（线程2开始释放已获得的lock2锁）`
线程2给LOCK2释放锁成功
线程2:线程退出`（线程2结束）`
线程1给LOCK2加锁成功`（线程1继续获取到lock2的锁）`
线程1:拿到两个lock，并完成了工作
线程1给LOCK1释放锁开始
线程1给LOCK1释放锁成功
线程1给LOCK2释放锁开始
线程1给LOCK2释放锁成功
线程1:线程退出
```

可通过中断线程来释放该线程已获得的锁，可避免线程死锁问题。

**condition网上有个很好的例子，很直观，代码如下**
{% highlight java %}
public class ReentrantLockConditionDemo {

    private Lock theLock = new ReentrantLock();
    // 消费者用判断条件
    private Condition full = theLock.newCondition();
    // 生产者用判断条件
    private Condition empty = theLock.newCondition();

    private static List<String> cache = new LinkedList<>();

    // 生产者线程任务
    public void put(String str) throws InterruptedException {
        Thread.sleep(200);
        // 获取线程锁
        theLock.lock();
        try {
            while (cache.size() != 0) {
                System.out.println("超出缓存容量.暂停写入.");
                // 生产者线程阻塞
                full.await();
                System.out.println("生产者线程被唤醒");
            }
            System.out.println("写入数据");
            cache.add(str);
            // 唤醒消费者
            empty.signal();
        } finally {
            // 锁使用完毕后不要忘记释放
            theLock.unlock();
        }
    }

    // 消费者线程任务
    public void get() throws InterruptedException {
        try {
            while (!Thread.interrupted()) {
                // 获取锁
                theLock.lock();
                while (cache.size() == 0) {
                    System.out.println("缓存数据读取完毕.暂停读取");
                    // 消费者线程阻塞
                    empty.await();
                }
                System.out.println("读取数据");
                cache.remove(0);
                // 唤醒生产者线程
                full.signal();
            }
        } finally {
            // 锁使用完毕后不要忘记释放
            theLock.unlock();
        }
    }

    public static void main(String[] args) {
        final ReentrantLockConditionDemo rd = new ReentrantLockConditionDemo();
        // 创建1个消费者线程
        for (int i = 0; i < 1; i++) {
            new Thread(() -> {
                try {
                    rd.get();
                } catch (InterruptedException e) {

                    e.printStackTrace();
                }
            }).start();
        }
        // 创建10个生产者线程
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                try {
                    rd.put("bread");
                } catch (InterruptedException e) {

                    e.printStackTrace();
                }
            }).start();
        }
    }
}
{% endhighlight %}

> ReentrantLock 保证了原子性、可见性、有序性

## 两者区别
- 都是重入锁
- 都是互斥锁、排它锁
- synchronized依赖于JVM实现，ReentrantLock是JDK实现。前者是系统帮你加锁，后者是要你自己写代码加锁。前者隐式加锁，反编译可见加锁代码；后者显示加锁，源文件可见加锁代码。
- synchronized优化前(jdk5.0)，性能不如ReentrantLock，引入偏向锁、轻量级锁（自旋锁）优化后，两者性能区别不大。
- synchronized代码简洁，不需要用户对锁做操作。ReentrantLock需要用户手动声明加锁、释放锁。
- synchronized功能较为单一，ReentrantLock功能更多。
