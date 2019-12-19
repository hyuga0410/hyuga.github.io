---
layout:       post
title:        "Jrebel破解教程"
subtitle:     "Jrebel本地破解教程"
date:         2019-12-19 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-26.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - jrebel
---

#### 教程来源

[纪莫]()_[Jrebel激活服务搭建](https://www.cnblogs.com/jimoer/p/11161339.html)

#### Jrebel
Jrebel是什么就不啰嗦了，神器自由妙用。

赞同原文作者文章中的一句话：**Jrebel很好用，也是离不开大家的支持，所以如果条件允许的话，还是建议大家购买正版的lisence。**

#### 教程

###### 搭建激活服务
1. 码云上下载开源的程序代码：[https://gitee.com/gsls200808/JrebelLicenseServerforJava](https://gitee.com/gsls200808/JrebelLicenseServerforJava)

2. 最便捷的方式是下载原作者的发行版：[https://gitee.com/gsls200808/JrebelLicenseServerforJava/releases](https://gitee.com/gsls200808/JrebelLicenseServerforJava/releases)

3. 发行版地址：[https://gitee.com/gsls200808/JrebelLicenseServerforJava/attach_files/135982/download](https://gitee.com/gsls200808/JrebelLicenseServerforJava/attach_files/135982/download)

4. 启动：`/user/../jdk/java -jar JrebelBrainsLicenseServerforJava-1.0-SNAPSHOT-jar-with-dependencies.jar`(默认端口8081)

5. 或指定端口启动：`/user/../jdk/java -jar JrebelBrainsLicenseServerforJava-1.0-SNAPSHOT-jar-with-dependencies.jar -p 8082`

启动后进程：
{% highlight java %}
[root@lvs213 init.d]# ps -ef|grep Jrebel
root     19583     1  1 11:20 pts/1    00:00:02 /usr/local/jdk-11/bin/java -server -Xms512m -Xmx512m -jar /data/www/platform-jar/JrebelBrainsLicenseServerforJava-1.0-SNAPSHOT-jar-with-dependencies.jar
root     20098 19294  0 11:22 pts/1    00:00:00 grep Jrebel
{% endhighlight %}

上述是最便捷的方式，然后只要到idea工具中激活即可，教程如下！

![](/img/2019/2019-12/jrebel-1.png)
![](/img/2019/2019-12/jrebel-2.png)
![](/img/2019/2019-12/jrebel-3.png)
![](/img/2019/2019-12/jrebel-4.png)

到这里就算是大功告成了。

#### Ps 

当然，你也可以下载项目源码进行构建发包，不过本人实操过程可能是中间哪个环节我搞错了，master分支源码打完包后启动找不到主类。

最后懒得折腾就直接用原作者的发行版jar包启动。

如果有人对自己打包构建执着的，也可以按gitee项目README.md文件进行操作，或者按本文教程来源作者[纪莫](https://www.cnblogs.com/jimoer/p/11161339.html)的文章进行操作。




