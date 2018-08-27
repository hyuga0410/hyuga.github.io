---
layout:       post
title:        "JVM内存结构2"
subtitle:     "详解Java虚拟机运行时内存结构"
date:         2018-08-22 22:42:05
author:       "Hyuga"
header-img:   "img/2018-08-22/head-top.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - god road
---

之前JVM简介文章中介绍到JVM内存划分有三部分，分别是：
- 类装载：`在JVM启动时或者在类运行时将需要的class加载到JVM`
- 运行时数据区：`JVM中数据交互内存区域`
- 执行引擎：`负责执行class文件中包含的字节码指令`

**本篇主要详解JVM运行时数据区的内存结构**

