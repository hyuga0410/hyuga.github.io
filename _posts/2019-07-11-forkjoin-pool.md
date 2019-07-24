---
layout:       post
title:        "多线程 ForkJoinPool"
subtitle:     "不一样的线程池"
date:         2019-07-11 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-19.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - java
---

## 多线程 ForkJoinPool

#### 特性
JDK 7 开始提供，`ForkJoinPool`是`ExecutorService`的实现类，也是一种线程池，但和`ThreadPool`不同的是，`ForkJoinPool`支持将一个任务拆分成多个"小任务"并行计算，再把多个"小任务"的结果合并成总的计算结果。

#### 原理
`ForkJoinPool`可充分发挥多核CPU的性能，把一个任务拆分后放到多个处理器核心上并行计算，都执行完成后，再将各个子任务的结果合并。

#### 使用方式
创建了ForkJoinPool实例之后，就可以调用ForkJoinPool的submit(ForkJoinTask<T> task) 或invoke(ForkJoinTask<T> task)方法来执行指定任务了。









