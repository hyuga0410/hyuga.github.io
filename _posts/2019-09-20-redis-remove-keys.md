---
layout:       post
title:        "Redis删除指定前缀Key的缓存集合"
subtitle:     "Redis删除指定前缀Key的缓存集合，指令批量删除"
date:         2019-09-20 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-10.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - redis
---

## 场景
项目改版上线，需要清除手机端之前的登录信息，强制重新登录。

## 方案

> 方案一

请求拦截器，只要检测不符合条件，清除登录缓存，前端跳转至登录页

- 好处：方便，几行代码的问题。
- 坏处：每次拦截都要做附加查询判断，而且附加代码什么时候移除也是个问题，可能移除后，移动端再登录，一样拦截不了。

> 方案二

清除redis缓存，使之重新登录

- 好处：高效快捷，删除所有符合条件的缓存数据，不需要植入代码逻辑。
- 坏处：有风险，需要连接redis服务器进行批量删除操作，可能存在一些代码逻辑判断异常的风险，需要经过全面测试，选好时间点才能在线上操作。

## 决定

> 采用第二个方案，具体操作如下。

- 连接redis服务器，进入服务器系统
- 进入redis目录，如`/opt/redis-4.0.14/src`
- 执行下面的代码，自行替换`key`前缀

{% highlight java %}
./redis-cli -h 10.152.2.205 -p 6379 keys "SESSION_BASE:SESSIONID:*" | xargs ./redis-cli -h 10.152.2.205 -p 6379 DEL 
{% endhighlight %}

**注意：**
- `SESSION_BASE:SESSIONID:*`是key，`*`是指查询所有以该key为前缀的keys
- `10.152.2.205` --> ip
- `6379` --> port
 
