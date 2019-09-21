---
layout:       post
title:        "Lambda循环对象去重"
subtitle:     "lambda表达式循环过滤特定条件去重"
date:         2019-09-21 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-12.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - java
---

## 场景
根据指定参数笛卡尔积混搭，可能会出现重复url，需要把list中有一样url属性的对象去重。

## 方案

最开始直观的想法是`for`循环，用一个`set`为url去重，符合条件的放入新的list中。

思路尚可，但是实现代码看起来很冗长。

故此用lambda实现！

{% highlight java %}
//汇总多线程结果 
List<List<QuestionDto>> result = ThreadPool.invokeAllTask(tasks); 

//将多线程返回结果List<List<QuestionDto>>转换为List<QuestionDto>
List<QuestionDto> resultCollection = new ArrayList<>();
ListUtil.optimize(result).forEach(resultCollection::addAll);

//使用lambda-filter去重
ListUtil.optimize(resultCollection).stream().filter(dto -> StringUtil.hasText(dto.getLoc())).filter(distinctByKey(QuestionDto::getLoc, result.size())).collect(Collectors.toList());

/** 
 * 定义函数方法，用于根据questionDto的loc字段去重
 * 不涉及到共享变量，没有线程安全问题
 * 为什么是这样写的，因为上面的filter是需要一个Predicate返回的参数的
 * 用concurrentHashMap里面的putIfAbsent进行排重
 */
private static <T> Predicate<T> distinctByKey(Function<? super T, Object> keyExtractor, int size) {
    Map<Object, Boolean> seen = new ConcurrentHashMap<>(size);
    return object -> seen.putIfAbsent(keyExtractor.apply(object), Boolean.TRUE) == null;
}
{% endhighlight %}

## 另一种实现方式

{% highlight java %}
List<QuestionDto> list = ListUtil.NEW();
list.add(new QuestionDto("test_1", DateUtil.now(), 0.1, ChangeFreq.WEEKLY));
list.add(new QuestionDto("test_2", DateUtil.now(), 0.2, ChangeFreq.WEEKLY));
list.add(new QuestionDto("test_1", DateUtil.now(), 0.3, ChangeFreq.WEEKLY));
list.add(new QuestionDto("test_4", DateUtil.now(), 0.4, ChangeFreq.WEEKLY));

List<QuestionDto> distinctList = list.stream().collect(
    Collectors.collectingAndThen(
        Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(QuestionDto::getLoc))), ArrayList::new
    )
);

System.out.println(distinctList);
{% endhighlight %}

第二种方式先汇集数据，然后筛选出符合loc唯一条件，组装出一个新的list返回。

## 实现参考

- [lambda实体属性去重，对实体的某个属性进行去重](https://blog.csdn.net/u011410529/article/details/66971027) 
- 原文作者：一年e度的夏天
- 来源：CSDN

 
