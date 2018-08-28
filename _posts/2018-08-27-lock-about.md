---
layout:       post
title:        "并发、锁的一些概念"
subtitle:     "浅显整合一些并发、锁相关的知识点"
date:         2018-08-27 22:42:05
author:       "Hyuga"
header-img:   "img/2018-08-27/head-top.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - lock
---

## 什么是并发？
多个进程或线程同时访问同一资源会产生并发问题。

> 例如银行存取款，两个人操作同一个账号，原余额100，一人存100，一人取50
>
> A看到余额100，加100，预期结果为余额200
>
> B看到余额100，减50，预期结果为余额50
>
> 正常结果余额应为：`100+100-50=150` 或者 `100-50+100=150`
>
> 但是当A/B同时操作时，没有并发处理机制时，结果就有可能是下面这两种情况

    A先提交：100+100=200，B再提交：100-50=50
    B先提交：100-50=50，A再提交：100+100=200

> 最终余额有可能为50，也可能为200
> 
> 这就是并发造成的问题！！！

简单说，并发就是多线程或者说多用户同时操作同一数据，造成系统数据不正确的现象。

## 什么是高并发？
顾名思义，高并发是并发的一种场景，意指大量并发请求，例如抢票，秒杀等场景。

## 并发造成的数据问题
- 丢失更新

    A读取数据，B同时读取同份数据，A更新数据，B覆盖更新数据，则A的更新丢失

- 不可重复读
    
    A读取数据，B同时读取同份数据，A更新数据，B再次重新读取数据，发现数据变了，重复读出来的数据不一样

- 脏数据

    A读取数据，A更新数据且事务未提交，B读取A更新后的数据，这时A抛出异常，数据回滚，B读取到的还是A更新后的数据

- 幽灵数据

    同脏数据类似

    A读取数据，A更新数据并提交事务，B读取A提交后的数据，A又对数据做了新一轮的更新，B此时读取到的数据是A上一次操作的数据

## 公平锁、非公平锁

- 公平锁(Fair)：加锁前检查是否有排队等待的线程，优先排队等待的线程，先来先得。

- 非公平锁(Nonfair)：加锁时不考虑排队等待问题，直接尝试读取锁，读取不到自动到队尾等待。

ReentrantLock锁内部提供了公平锁与分公平锁内部类之分，默认是非公平锁，如：
```
public ReentrantLock() {
    sync = new NonfairSync();
}
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

## 互斥锁、读写锁
- 互斥锁：指的是一次最多只能有一个线程持有的锁
    - 在jdk1.5之前, 我们通常使用synchronized机制控制多个线程对共享资源的访问。
    - 在jdk1.5之后, Lock提供了比synchronized机制更广泛的锁定操作。

Lock和synchronized机制的主要区别:

    synchronized机制提供了对与每个对象相关的隐式监视器锁的访问，并强制所有锁读取和释放均要出现在一个块结构中，当读取了多个锁时, 它们必须以相反的顺序释放。
    synchronized机制对锁的释放是隐式的，只要线程运行的代码超出了synchronized语句块范围，锁就会被释放。而Lock机制必须显式的调用Lock对象的unlock()方法才能释放锁，这为读取锁和释放锁不出现在同一个块结构中，以及以更自由的顺序释放锁提供了可能。

- 读写锁：ReadWriteLock接口及其实现类ReentrantReadWriteLock，默认情况下也是非公平锁。

ReentrantReadWriteLock中定义了2个内部类，ReentrantReadWriteLock.ReadLock和ReentrantReadWriteLock.WriteLock，分别用来代表读取锁和写入锁，ReentrantReadWriteLock对象提供了readLock()和writeLock()方法，用于读取读取锁和写入锁。

java.util.concurrent.locks.ReadWriteLock接口允许一次读取多个线程，但一次只能写入一个线程：

- 读锁 - 如果没有线程锁定ReadWriteLock进行写入，则多线程可以访问读锁。

- 写锁 - 如果没有线程正在读或写，那么一个线程可以访问写锁。

其中：

读取锁允许多个reader线程同时持有，而写入锁最多只能有一个writer线程持有。
读写锁的使用场合是：读取数据的频率远大于修改共享数据的频率。在上述场合下使用读写锁控制共享资源的访问，可以提高并发性能。

如果一个线程已经持有了写入锁，则可以再持有读锁。相反，如果一个线程已经持有了读取锁，则在释放该读取锁之前，不能再持有写入锁。

可以调用写入锁的newCondition()方法读取与该写入锁绑定的Condition对象，此时与普通的互斥锁并没有什么区别，但是调用读取锁的newCondition()方法将抛出异常。

## 悲观锁
悲观锁（Pessimistic Lock），顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。

悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。

Java `synchronized` 就属于悲观锁的一种实现，每次线程要修改数据时都先获得锁，保证同一时刻只有一个线程能操作数据，其他线程则会被block。

## 乐观锁
乐观锁（Optimistic Lock），顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在提交更新的时候会判断一下在此期间别人有没有去更新这个数据。乐观锁适用于读多写少的应用场景，这样可以提高吞吐量。

乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。

乐观锁一般来说有以下2种方式：

- 使用数据版本（Version）记录机制实现，这是乐观锁最常用的一种实现方式。何谓数据版本？即为数据增加一个版本标识，一般是通过为数据库表增加一个数字类型的 “version” 字段来实现。当读取数据时，将version字段的值一同读出，数据每更新一次，对此version值加一。当我们提交更新的时候，判断数据库表对应记录的当前版本信息与第一次取出来的version值进行比对，如果数据库表当前版本号与第一次取出来的version值相等，则予以更新，否则认为是过期数据。
- 使用时间戳（timestamp）。乐观锁定的第二种实现方式和第一种差不多，同样是在需要乐观锁控制的table中增加一个字段，名称无所谓，字段类型使用时间戳（timestamp）, 和上面的version类似，也是在更新提交的时候检查当前数据库中数据的时间戳和自己更新前取到的时间戳进行对比，如果一致则OK，否则就是版本冲突。

Java JUC中的atomic包就是乐观锁的一种实现，AtomicInteger 通过CAS（Compare And Set）操作实现线程安全的自增。

## 悲观、乐观锁的区别
- 乐观锁的思路一般是表中增加版本字段或时间戳等，更新时where语句中增加版本的判断，算是一种CAS（Compare And Swep）操作，商品库存场景中number（库存量）起到了版本控制（相当于version）的作用（ AND number=#{number}）。
- 悲观锁之所以是悲观，在于他认为本次操作会发生并发冲突，所以一开始就对商品加上锁（SELECT ... FOR UPDATE），然后就可以安心的做判断和更新，因为这时候不会有别人更新这条商品库存。

## MySQL隐式和显示锁定
MySQL InnoDB采用的是两阶段锁定协议（two-phase locking protocol）。在事务执行过程中，随时都可以执行锁定，锁只有在执行 COMMIT或者ROLLBACK的时候才会释放，并且所有的锁是在同一时刻被释放。前面描述的锁定都是隐式锁定，InnoDB会根据事务隔离级别在需要的时候自动加锁。

另外，InnoDB也支持通过特定的语句进行显示锁定，这些语句不属于SQL规范：
```
SELECT ... LOCK IN SHARE MODE
SELECT ... FOR UPDATE
```

## 加不加锁的区别
```
UPDATE tb_product_stock SET number=number-1 WHERE product_id=#{productId}
```
这种sql在并发情况下，可能出现错误数据

使用悲观锁
```
UPDATE tb_product_stock SET number=number-1 WHERE product_id=#{productId} for update
```

使用乐观锁
```
UPDATE tb_product_stock SET number=number-1 WHERE product_id=#{productId} and number=#{number}
```

![](/img/2018-08-27/lock-about.png)

## 参考资料

[Java中的锁-悲观锁、乐观锁，公平锁、非公平锁，互斥锁、读写锁](https://blog.csdn.net/loongshawn/article/details/76985272)

[MySQL 乐观锁与悲观锁](https://www.jianshu.com/p/f5ff017db62a)

[数据库并发操作会带来哪些问题及原因](https://blog.csdn.net/echo_sy/article/details/64137282)
