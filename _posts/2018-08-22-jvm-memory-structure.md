---
layout:       post
title:        "JVM内存结构"
subtitle:     "详解Java虚拟机运行时内存结构"
date:         2018-08-22 22:42:05
author:       "Hyuga"
header-img:   "img/2018-08-22/head-top.jpeg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - god road
---

## 什么是[JVM][6]
引用百度百科的解释：
> JVM是Java Virtual Machine（Java虚拟机）的缩写，JVM是一种用于计算设备的规范，它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。
>
> Java语言的一个非常重要的特点就是与平台的无关性。而使用Java虚拟机是实现这一特点的关键。一般的高级语言如果要在不同的平台上运行，至少需要编译成不同的目标代码。而引入Java语言虚拟机后，Java语言在不同平台上运行时不需要重新编译。Java语言使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需生成在Java虚拟机上运行的目标代码（字节码），就可以在多种平台上不加修改地运行。Java虚拟机在执行字节码时，把字节码解释成具体平台上的机器指令执行。这就是Java的能够“一次编译，到处运行”的原因。

## JVM运行原理
JVM是java的核心和基础，也是Java能跨平台的关键。

跨平台实现原理：
* 不同平台有不同的Java虚拟机
* java编译器只需要把源码编译成.class字节码文件，然后交由虚拟机将每一条指令翻译成不同平台的机器码，通过对应平台的JVM运行即可





##

## 参考资料:
**《深入理解Java虚拟机》**

   链接：[Java虚拟机的内存组成以及堆内存介绍-HollisChuang's Blog][1]

   链接：[Java堆和栈看这篇就够 - Johnny-Zhuang's Technology Blog][2]

   链接：[Java虚拟机的堆、栈、堆栈如何去理解？ - 知乎][3]

   链接：[Java 内存之方法区和运行时常量池 - 漠然的博客 | mritd Blog][4]

   链接：[从0到1起步-跟我进入堆外内存的奇妙世界 - 简书][5]


   [1]:http://www.hollischuang.com/archives/80
   [2]:https://iamjohnnyzhuang.github.io/java/2016/07/12/Java%E5%A0%86%E5%92%8C%E6%A0%88%E7%9C%8B%E8%BF%99%E7%AF%87%E5%B0%B1%E5%A4%9F.html
   [3]:https://www.zhihu.com/question/29833675
   [4]:https://mritd.me/2016/03/22/Java-%E5%86%85%E5%AD%98%E4%B9%8B%E6%96%B9%E6%B3%95%E5%8C%BA%E5%92%8C%E8%BF%90%E8%A1%8C%E6%97%B6%E5%B8%B8%E9%87%8F%E6%B1%A0/
   [5]:https://www.jianshu.com/p/50be08b54bee

   [6]:https://baike.baidu.com/item/JVM/2902369?fr=aladdin


