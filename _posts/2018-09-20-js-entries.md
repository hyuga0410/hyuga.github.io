---
layout:       post
title:        "原生JS对象方法Object.entries(obj)"
subtitle:     "简单记录下js的几个对象扩展方法"
date:         2018-09-20 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-20.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - js
---
## 前言

项目前后端分离后，JS撸得很少很少，最近改老项目发现有个js很有趣，功能是拼接一个对象的属性和值，作为一个get的参数，代码如下：

{% highlight js %}
var paramArray = {'boy':'hyuga', 'girl':'HelloKitty'};
var param = Object.entries(paramArray)
                .map(function (i) {
                    return i.join('=');
                })
            .join('&');
console.log(param);

输出：boy=hyuga&girl=HelloKitty
{% endhighlight %}

上面这段JS初一看一脸懵逼，链式、函数式、还有个什么entries？

接下来就先了解下JS提供的几个对象扩展方法

- `Object.keys(obj)` 遍历取出对象的属性名，返回数组
- `Object.values(obj)` 遍历取出对象的值，返回数组
- `Object.entries(obj)` 遍历将对象各个属性和值组成数组，再放入一个新数组，最终返回新数组

### Object.keys(obj)
{% highlight js %}
var obj = { foo: 'bar', baz: 42 };
console.log(Object.keys(obj));

输出：["foo", "baz"]
{% endhighlight %}

当对象的属性是数值的情况下，keys()取出的数组会自动排序
{% highlight js %}
var obj1 = { 2: 'bar', 1: 42 };
console.log(Object.keys(obj1));
输出：["1", "2"]
{% endhighlight %}

### Object.values(obj)
{% highlight js %}
var obj = { foo: 'bar', baz: 42 };
console.log(Object.values(obj));
输出：["bar", 42]

当属性是数值时，最终返回数组也会根据value排序.
{% endhighlight %}


### Object.entries(obj)
{% highlight js %}
var obj = { foo: 'bar', baz: 42, abc: true };
console.log(Object.entries(obj));
输出：
[Array(2), Array(2), Array(2)]
0: (2) ["foo", "bar"]
1: (2) ["baz", 42]
2: (2) ["abc", true]

当属性是数值时，最终返回数组也会根据key排序.
{% endhighlight %}

## 解析
再回过头来看看上面的语句
{% highlight js %}
var paramArray = {'boy':'hyuga', 'girl':'HelloKitty'};
Object.entries(paramArray)
    .map(function (i) {
        return i.join('=');
    })
.join('&');
{% endhighlight %}

`Object.entries(paramArray)` 将paramArray拆解成[['boy', 'hyuga'], ['girl', 'HelloKitty']]

`.map(function (i) { return i.join('='); })` 函数式编程，对上面转换后的数组遍历操作元素，i是数组元素对象，如['boy', 'hyuga']，转换成 ["boy=hyuga", "girl=HelloKitty"]

`.join('&')` 将上面转换好的数组再用&拼接成字符串

这种拼接写法简洁了很多，不需要用for...in...遍历拼接了。

本文只是简单记录了下工作中遇到的情况，和JS对象扩展中的几个方法。真的是学海无涯，什么都在快速发展。