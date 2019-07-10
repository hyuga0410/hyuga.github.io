---
layout:       post
title:        "奇怪的问题汇总"
subtitle:     "Strange question"
date:         2019-07-10 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-12.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - 疑难杂症
---

#### java.lang.NoClassDefFoundError: ch/qos/logback/classic/spi/ThrowableProxy

Spring Boot 报错

经过多次测试得出原因： 服务器运行时，不要上传jar包，一定要把服务停止掉，再上传jar。

起因：本地起了jar包服务，然后服务没停掉，直接重打了jar，再访问服务会报上面的错误。

打完jar包服务重启就好了。

解决方案来自：[https://juejin.im/post/5a3341dbf265da430a50a003](https://juejin.im/post/5a3341dbf265da430a50a003)


#### java.lang.UnsatisfiedLinkError: /opt/jdk-11/lib/libfontmanager.so: libfreetype.so.6: 无法打开共享对象文件: 没有那个文件或目录

起因：项目非前后端分离，后端调用google服务生成图形验证码，报错。

分析：一开始以为是jdk问题，排查后是因为linux系统的问题，`CentOS release 6.10 (Final)`系统缺失了`libfreetype.so`. 

可能是这个版本的系统本身缺失，也可能是以前系统被修改过一些东西。个人猜测是google图像工具包调用了jdk11的libfontmanager.so字体库，libfontmanager.so字体库又依赖了系统的libfreetype字体库。

{% highlight java %}
<dependency>
    <groupId>com.github.penggle</groupId>
    <artifactId>kaptcha</artifactId>
    <version>2.3.2</version>
</dependency>
{% endhighlight %}

后来把服务迁移到`CentOS release 6.4 (Final)`上就可以了，并且再往上的系统版本也都正常。