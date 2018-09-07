---
layout:       post
title:        "Java线程池知识点"
subtitle:     "详解java线程池的特性以及优劣"
date:         2018-08-28 22:42:05
author:       "Hyuga"
header-img:   "img/2018/2018-08/head-top-7.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - thread
---

本篇主要讲述线程池相关的一些知识点，包括线程池的创建方式、参数详解等。

## 进程与线程
- 进程：是一个程序在其自身的地址空间中的一次执行活动。一个应用程序可浅显理解为一个进程，一个进程里面至少有一个主线程。
- 线程：进程中的一个单一的连续控制流程。一个进程可以拥有多个线程。

## 为什么要用线程池
假设：系统每个请求都要记录操作日志，单个任务处理时间很短，但请求数据巨大。这种情况采用线程处理会怎样？
频繁创建和销毁线程，造成大量的系统资源浪费。

- 频繁创建和销毁新线程的开销很大，在此耗费的时间和资源可能比实际处理的用户请求还要多。
- 除了创建和销毁线程的开销之外，活动的线程也消耗系统资源（线程的生命周期）
- 在一个JVM 里创建太多的线程可能会导致系统由于过度消耗内存而用完内存或“切换过度”。

so，怎样才能减少线程的耗损，提高其复用性和效率？

没错，线程池是你的最佳选择！！！

使用线程池的场景：
- 单个任务处理的时间比较短
- 将需处理的任务的数量大

## 什么是线程池
`线程池` 线程管理中控系统

## 线程池的优劣
优点：
- 提高资源利用率：线程池可重复利用已经创建了的线程，大大减少了频繁创建销毁锁造成的系统资源开销
- 提交响应速度：可设置核心线程数，但又请求到来，若核心线程无被调用，则可直接为核心线程分配任务
- 具有可管理性：线程池可自行设置参数，如核心线程数、队列模式、定时、定期、点线程、并发数控制等

缺点：
网上搜了很多资料也没怎么明确说出线程池有啥缺点，可能是利远大于弊吧。

## 常见线程池
`newSingleThreadExecutor`

![](/img/2018/2018-08/newSingleton.png)
{% highlight java %}
return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>()));
{% endhighlight %}
- 单一的线程池，该线程池的核心线程数和最大线程数都是1，且keepAliveTime为0，该线程池永远保持一条线程，且不回收
- 队列采用：一个由链表结构组成的有界阻塞队列

`newFixedThreadPool`

![](/img/2018/2018-08/newFixedThreadPool.png)
{% highlight java %}
return new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>());
{% endhighlight %}
- 固定的线程池，拥有固定数量的线程池，核心线程数等于最大线程数，且keepAliveTime为0，该线程池永远保持固定数量的线程数，且不回收
- 队列采用：一个由链表结构组成的有界阻塞队列

`newCachedThreadPool`

![](/img/2018/2018-08/newcachedthreadpool.png)
{% highlight java %}
return new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>());
{% endhighlight %}
- 缓存的线程池，核心线程数为0，最大线程数为Integer.MAX_VALUE
- 队列采用：一个不存储元素的阻塞队列

`newScheduledThreadPool`

![](/img/2018/2018-08/newScheduledThreadPool.png)
{% highlight java %}
super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS, new DelayedWorkQueue());
{% endhighlight %}
- 定时的线程池，可执行定时或定期任务的
- 队列采用：延迟工作队列

---
`ThreadFactory` 可用于定制线程池名称
{% highlight java %}
final ThreadFactory namedThreadFactory = new ThreadFactoryBuilder().setNameFormat("HYUGA-THREAD-POOL-%d").build();
{% endhighlight %}

## 线程池参数详解
{% highlight java %}
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler)

{% endhighlight %}

- corePoolSize：核心线程数量，线程池初始化的时候，就创建好相当数量的线程
- maximumPoolSize：最大线程数量
- keepAliveTime：非核心线程的存活时间，当核心线程都用上后，后续请求将有可能创建非核心线程（newSingleThreadExecutor、newFixedThreadPool不会创建非核心线程），
非核心线程执行完任务后，闲置时间超过keepAliveTime，则会被回收掉。如果ThreadPoolExecutor的allowCoreThreadTimeOut属性设置为true，则该参数也表示核心线程的超时时长。
    - executorService.allowCoreThreadTimeOut(true);
        - true：keepAliveTime同样影响核心线程，最终线程池线程数量为0
        - false：keepAliveTime只影响非核心线程
- unit：keepAliveTime的时间单位。
- workQueue：任务队列，该队列主要用来存储已经被提交但是尚未执行的任务。
- threadFactory：为线程池提供自定义功能，一般用来自定义线程池的名称。
- handler：拒绝策略，当线程无法执行新任务时（一般是由于线程池中的线程数量已经达到最大数或者线程池关闭导致的），默认情况下，当线程池无法处理新线程时，会抛出一个RejectedExecutionException。

线程池还有一个隐藏参数概念，就是非核心线程：

`非核心线程数` = `最大线程数` - `核心线程数`

- 任务来了，当正在执行任务的线程数 < 核心线程数，则任务直接交由核心线程执行
- 任务来了，当核心线程都在执行任务，并且队列workQueue未满，则任务会先放到队列中等待执行
- 任务来了，当核心线程都在执行任务，并且队列workQueue也满了，则开启一个非核心线程执行任务
- 任务来了，当核心线程都在执行任务，并且队列workQueue也满了，也达到了线程池允许的最大线程数量，则触发handler拒绝策略默认抛出RejectExecutionException异常

**workQueue**
> 有界表示数组或链表有大小，无界表示无上限

- 有界队列
    - ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。
    - LinkedBlockingQueue ：一个由链表结构组成的有界阻塞队列。
- 无界队列
    - PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。
    - DelayQueue： 一个使用优先级队列实现的无界阻塞队列。
    - SynchronousQueue： 一个不存储元素的阻塞队列。
    - LinkedTransferQueue： 一个由链表结构组成的无界阻塞队列。【MAX:2147483647】
    - LinkedBlockingDeque： 一个由链表结构组成的双向阻塞队列。

**handler**
> 线程无法执行新任务时的拒绝策略

- ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
- ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
- ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
- ThreadPoolExecutor.CallerRunsPolicy：只要线程池不关闭，该策略直接在调用者线程中，运行当前被丢弃的任务

个人认为这4中策略不友好，最好自己定义拒绝策略，实现RejectedExecutionHandler接口。

## 墙裂推荐的单例线程池
线程池为什么要使用单例实现？

如果不单例，随处创建线程池，那还用线程池干嘛？！

[Hollis大神](http://www.hollischuang.com/)墙裂推荐的单例实现方式，枚举单例。

下面将使用枚举方式实现单例线程池
{% highlight java %}
public class ThreadPool{
    private static ExecutorService executorService = null;

    private enum ThreadPoolSingleton {
        INSTANCE;

        private ExecutorService threadPoolService;

        ThreadPoolSingleton() {
            final ThreadFactory namedThreadFactory = new ThreadFactoryBuilder().setNameFormat("HYUGA-THREAD-POOL-%d").build();
            threadPoolService = new HyugaThreadPoolExecutor(2, 5, 60L,
                    TimeUnit.SECONDS, new LinkedBlockingQueue<>(), namedThreadFactory, new ThreadPoolExecutor.AbortPolicy());
        }

        public ExecutorService getSingleton() {
            return threadPoolService;
        }
    }

    private static ExecutorService getPool() {
        return ThreadPoolSingleton.INSTANCE.getSingleton();
    }

}
{% endhighlight %}

顺便也贴下采用静态内部类方式实现的单例定时调度线程池
{% highlight java %}
public class ThreadScheduledPool {
    private static ScheduledExecutorService scheduledExecutorService = null;

     private static class ThreadPoolHolder {
        private static ThreadFactory namedThreadFactory = new ThreadFactoryBuilder().setNameFormat("HYUGA-THREAD-SCHEDULED-POOL-%d").build();
        private static final ScheduledExecutorService INSTANCE = Executors.newScheduledThreadPool(5, namedThreadFactory);
    }

    private static synchronized ScheduledExecutorService getPool() {
        return ThreadPoolHolder.INSTANCE;
    }

}
{% endhighlight %}

用枚举实现单例的优点：
- 枚举写法更简单
    - 枚举单例不需要`synchronized`等关键字修饰
- 枚举实现更安全
    - 传统单例一旦实现了序列化接口，单例就被破坏了，可以反序列化生成一个新的实例
    - 枚举单例的序列化方式不同，JVM初次加载就创建实例，这个过程是thread-safe的，JVM的类装载机制保证了枚举单例的线程安全和序列化安全

