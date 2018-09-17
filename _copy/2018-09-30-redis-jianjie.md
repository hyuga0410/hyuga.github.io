---
layout:       post
title:        "MySQL的一些小优化"
subtitle:     "一些优化建议和sql执行计划"
date:         2018-09-11 22:42:05
author:       "Hyuga"
header-img:   "img/2018/2018-09/head-top-4.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - redis
---



## redis数据结构
> 类似于Map<K, V>
> K是字符串类型key，唯一性
> V是key对应的值，redis中有5种不同的V类型

- string（字符串）
- list（列表）
- set（集合）
- hash（哈希）
- zset（有序集合）



## string
V是字符串类型Value，如果是对象则使用Json序列化成字符串，常用于存储Json序列化后的当前登录用户信息。

此中数据结构的string字符串是动态字符串，类似于ArrayList，当字符串长度小于1M，扩容翻一倍，超过1M后，一次扩容最多1M。

字符串最大长度为512M。

##