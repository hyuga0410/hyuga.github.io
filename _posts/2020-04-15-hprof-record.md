---
layout:       post
title:        "Java之hprof分析"
subtitle:     "Java之使用JProfiler软件分析hprof文件"
date:         2020-04-15 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-18.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - java
---

## 前言
线上API服务挂了两次，第一次没来得及打印堆栈信息就被迫重启服务，结果等了没几天，服务又挂了。

## 处理方案

执行`ps -ef|grep service-xxx`，获取到服务进程pid【269000】

执行`jmap -dump:format=b,file=/log/hyuga/dump/20200415-jvm.hprof 269000`生成`20200415-jvm.hprof`文件

从服务器上下载该文件，`Mac OS`上安装软件【JProfiler】，然后用该软件打开`20200415-jvm.hprof`文件。

![](/img/2020/2020-04/java-jmap-1.png)

问题很明显了。。。

附上下载地址：[JProfiler](https://www.jb51.net/softs/609957.html)

至于使用教程，自行网上搜索吧。我也是小白。




