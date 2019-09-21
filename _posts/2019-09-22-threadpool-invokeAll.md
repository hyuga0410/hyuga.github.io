---
layout:       post
title:        "批量获取多条线程的执行结果"
subtitle:     "如何批量获取多条线程的执行结果，简化代码"
date:         2019-09-22 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-14.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - java
---

## 场景
一个list，拆分成5份，每份数据放入线程池，返回future。

5个future任务，5个get方法。代码实在不雅观。

如下：

{% highlight java %}
List<Integer> list = ListUtil.NEW(1, 2, 3, 4, 5, 6);
List<List<Integer>> lists = ListUtil.splitList(list, 3);
Future<?> future1 = ThreadPool.submit(() -> {
List<Integer> integers = lists.get(0);
integers.forEach(System.out::println);
});
Future<?> future2 = ThreadPool.submit(() -> {
List<Integer> integers = lists.get(1);
integers.forEach(System.out::println);
});
Future<?> future3 = ThreadPool.submit(() -> {
List<Integer> integers = lists.get(2);
integers.forEach(System.out::println);
});
try {
future1.get();
future2.get();
future3.get();
} catch (InterruptedException | ExecutionException e) {
e.printStackTrace();
}
{% endhighlight %}

## 改造

{% highlight java %}
List<Integer> sourceList = ListUtil.NEW(1, 2, 3, 4, 5, 6);
List<List<Integer>> lists = ListUtil.splitList(sourceList, 3);
List<Callable<List<Integer>>> tasks = ListUtil.NEW();

lists.forEach(listTemp -> {
    Callable<List<Integer>> task = () -> {
        List<Integer> result = ListUtil.NEW();
        ListUtil.optimize(listTemp).forEach(x -> result.add(x + 1));
        return result;
    };
    ThreadPool.submit(task);
    tasks.add(task);
});

List<List<Integer>> result = ThreadPool.invokeAllTask(tasks);
System.out.println(result);
{% endhighlight %}

相关代码：

{% highlight java %}
public static <T> Future<T> submit(Callable<T> task) {
    return getPool().submit(task);
}

public static <T> List<T> invokeAllTask(List<Callable<T>> tasks) {
    List<Future<T>> futureList;
    List<T> results = new ArrayList<>(tasks.size());
    try {
        futureList = executorService.invokeAll(tasks);

        for (Future<T> future : futureList) {
            results.add(future.get());
        }
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
    return results;
}
{% endhighlight %}

## 参考

- [Java并发编程的艺术(九)——批量获取多条线程的执行结果](https://blog.csdn.net/u010425776/article/details/54580710)
- 作者：凌澜星空
- 来源：CSDN