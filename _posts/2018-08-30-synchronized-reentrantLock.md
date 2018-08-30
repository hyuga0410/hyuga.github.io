---
layout:       post
title:        "Synchronized与ReentrantLock"
subtitle:     "浅析Synchronized与ReentrantLock"
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

首先梳理一下概念性的东西

## 相关概念
- 公平锁
    多个线程等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁。

- 非公平锁
    多个线程等待同一个锁时，不按照申请锁的时间顺序获得锁，而是根据cpu调度随机分配。

- 可重入锁
    加锁代码块只允许一个线程获得对象锁，当线程取锁成功，则加锁对象上的加锁数量计数器+1，反之释放锁则-1，允许同个线程多次入锁，所以叫可重入锁。

- 排它锁、互斥锁
    同一时间只允许一个线程获得对象锁，进行操作，其他线程阻塞在外等待锁释放。

## synchronized
`synchronized` 是Java提供的一个并发控制的关键字。用于方法加锁和代码块加锁以实现同步，防止线程并发。

- 非公平锁
- 可重入锁

`synchronized` 是可重入锁。编译synchronized修饰的代码，可以看到同步块的代码前后添加了`monitorenter`、`monitorexit`这两个字节码指令。
jvm执行到`monitorenter`的时候，会尝试获取对象锁，如果获取不到，则放弃当前拥有的时间片，等下一个时间片再尝试获取锁；如果获取到锁，则执行包含的代码块，执行到`monitorexit`的时候，则释放锁。

每次执行`monitorenter`进入代码块，则加锁对象上的锁计数器+1，每次执行`monitorexit`则加锁对象上的所计数器-1.当计数器为0则表示锁已被释放。

**`synchronized`在JVM中的体现**
- `同步方法`：JVM采用`ACC_SYNCHRONIZED`标记符来实现同步。

- `同步代码块`：JVM采用`monitorenter`、`monitorexit`两个指令来实现同步。

#### `synchronized`怎么保证原子性？

> 线程是CPU调度的基本单位，cpu有时间片的概念，会根据不同的调度算法进行线程调度。
>
> 假如：cpu将1s分割为10等份，当线程1消耗完自己那一等份的时间后，就暂时失去cpu的使用权，当多线程争抢cpu时间片的时候，线程1操作一半，线程2又继续操作，原子性问题就发生了。

上面说到的`ACC_SYNCHRONIZED`、`monitorenter`、`monitorexit`就是JVM用来保证原子性的。

通过指令，jvm保证被`synchronized`所修饰的代码在同一时间内只能被一个线程访问，只要未完成前不释放锁，其他线程就算获取到cpu时间片，也无法执行修饰的代码。

#### `synchronized`怎么保证可见性？
> 系统变量都存在于主内存，cpu的性能远高于内存性能，同样的时间cpu可以比内存读写做更多的事，所以计算机中使用了高速缓存作为cpu和朱内存之间的衔接。cpu操作高速缓存，高速缓存在适当的时候再同主内存同步。
>
> 在多线程场景下，每个线程从高速缓存中取到的数据是互不可见的，这将会造成线程中高速缓存与主内存数据不同步，造成数据错误。
>
> 假如：主内存有变量i=1，线程1从高速缓存中拿到的i为1，线程2也从高速缓存中拿到i为1，线程2将i=2更新会主内存了，但是线程1还是继续对i=1进行操作。

内存模型JMM规定了所有变量都存在主内存，每条线程有自己的工作内存，工作内存中存放了该线程所用到的存在于主内存的变量副本，线程内的所有操作都是操作该副本，而不是直接操作主内存。
不同线程间的工作内存互不可见，变量传递都通过自身的工作内存和主内存进行同步。

使用`synchronized`加锁对象后，线程1对工作内存处理完后，更新回主内存，释放锁后，线程2再从主内存中拷贝最新的变量值到工作内存进行操作，所以，这也就实现了可见性。

#### `synchronized`怎么保证有序性？
synchronized关键字保证同一时刻只允许一条线程操作。

## ReentrantLock
java.util.concurrent包下提供的互斥锁、可重入锁

> 实现原理：
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

{% highlight java %}
Lock lock = new ReentrantLock();
lock.lock();
...
lock.unlock();
{% endhighlight %}

ReentrantLock待续！！！

## 两者区别
- 都是可重入锁
- 都是互斥锁、排它锁
- synchronized依赖于JVM实现，ReentrantLock是JDK实现。前者是系统帮你加锁，后者是要你自己写代码加锁。前者隐式加锁，反编译可见加锁代码；后者显示加锁，源文件可见加锁代码。
- synchronized优化前，性能不如ReentrantLock，引入偏向锁、轻量级锁（自旋锁）优化后，两者性能区别不大。
- synchronized代码简洁，不需要用户对锁做操作。ReentrantLock需要用户手动声明加锁、释放锁。
- synchronized功能较为单一，ReentrantLock功能更多。

## 什么是CAS(Compare and Swap)


