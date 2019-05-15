---
layout:       post
title:        "Tomcat技巧"
subtitle:     "tomcat使用过程中遇到的一些问题和小技巧记录"
date:         2019-05-10 18:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-22.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - tomcat
---

## 指定tomcat运行时JDK版本
- 解压tomcat包，进入bin目录，找到setclasspath.sh文件
- 在文件最开始的地方加入以下代码！

> linux

{% highlight java %}
export JAVA_HOME=/opt/jdk1.8.0_121
export JRE_HOME=/opt/jdk1.8.0_121/jre
{% endhighlight %}

> windows

{% highlight java %}
set JAVA_HOME=D:\Program Files\Java\jdk7\jdk1.7.0_51
set JRE_HOME=D:\Program Files\Java\jdk7\jre7
{% endhighlight %}

![](/img/2019/2019-05/tomcat-1.png)

修改了setclasspath文件之后，tomcat在启动时便使用设定的JDK。

**但是为什么这样设置之后就可以呢？原理如下：**

我们都知道启动tomcat可以通过运行bin下的startup.bat，startup.bat会调用catalina.bat文件，而catalina.bat会调用setclasspath.bat文件来获取JAVA_HOME和JRE_HOME这两个环境变量的值，因此若要在tomcat启动时指向特定的JDK，则需在setclasspath.bat文件的开头处加上JAVA_HOME和JRE_HOME。

基于上面的运行方式，还有第二种修改方式，如下：

1、修改tomcat/bin/catalina.bat，增加 set JAVA_HOME=D:\Program Files\Java\jdk7\jdk1.7.0_51

2、修改tomcat/bin/setclasspath.bat，同样增加

set JAVA_HOME=D:\Program Files\Java\jdk7\jdk1.7.0_51

set JRE_HOME=D:\Program Files\Java\jdk7\jre7

**这两种方式使用任何一种都可以实现修改tomcat的依赖JDK环境，同时可以不配置JDK的环境变量。**

上述内容来自：[http://www.cnblogs.com/teach/p/6086867.html](http://www.cnblogs.com/teach/p/6086867.html)

## 配置tomcat启动端口
1. vim conf/server.xml
2. 找到如下代码！
```xml
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```
3. 把8080改成你想要的端口号就好了！

## 直接通过域名访问

> Tomcat部署web项目,如何直接通过域名访问,不加项目名称

1. vim conf/server.xml
2. 找到如下代码！
```xml
<Host appBase="webapps">

 .....

</Host>
```
3. 在Host标签中假如如下元素
```xml
<Context path="" reloadable="false" docBase="dubbo-monitor"/>
```
dubbo-monitor是webapps目录下的文件目录名，也就是要启动的项目名
4. 如果上面的方式无效，就改用全路径
```xml
<Context path="" reloadable="false" docBase="/opt/tomcat-dubbo-monitor/webapps/dubbo-monitor"/>
```
5. 搞定，最终配置如下！
```xml
<Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">

<Context path="" reloadable="false" docBase="dubbo-monitor"/>

<!-- SingleSignOn valve, share authentication between web applications
     Documentation at: /docs/config/valve.html -->
<!--
<Valve className="org.apache.catalina.authenticator.SingleSignOn" />
-->

<!-- Access log processes all example.
     Documentation at: /docs/config/valve.html
     Note: The pattern used is equivalent to using pattern="common" -->
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
       prefix="localhost_access_log" suffix=".txt"
       pattern="%h %l %u %t &quot;%r&quot; %s %b" />

</Host>
```
