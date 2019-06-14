---
layout:       post
title:        "SpringBoot2.x TimeZone问题"
subtitle:     "SpringBoot2.x TimeZone时区问题"
date:         2019-06-14 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-10.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - SpringBoot
---

## 问题描述

项目SpringBoot版本由`1.5.13.RELEASE`升级到`2.1.3.RELEASE`后，发现dao层插入和查出的时间相关字段，时间全都和数据库里存储的值对应不上，如下：

{% highlight java %}

数据库存储的值：2019-06-13 15:22:03 

代码查询出来的值：2019-06-14 04:22:03

{% endhighlight %}

## 解决过程

> 更改数据库连接参数

`basic.mysql.url=jdbc:mysql:replication://x.x.x.x,x.x.x.x/xx?useSSL=false&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useLegacyDatetimeCode=false&serverTimezone=UTC`
   
更改后，返回时间对比如下：

{% highlight java %}

数据库存储的值：2019-06-13 15:22:03 

代码查询出来的值：2019-06-13 23:22:03

{% endhighlight %}

变成了典型的 +8 问题。。。

---

> 配置启动类参数

{% highlight java %}

在启动类加上

@PostConstruct 
void setDefaultTimezone() {
    TimeZone.setDefault(TimeZone.getTimeZone("Asia/Shanghai")); 
}  
 
在启动类 启动run方法里加上

public static void main(String[] args) { 　　 
    TimeZone.setDefault(TimeZone.getTimeZone("Asia/Shanghai")); 　　 
    //TimeZone.setDefault(TimeZone.getTimeZone("GMT+8")); 　　 
    //TimeZone.setDefault(TimeZone.getTimeZone("UTC")) 
    SpringApplication.run(BaseMicroServiceApplication.class, args); }

{% endhighlight %}

很可惜，无效！

---

> 在应用配置文件配置

在`application.yml`或`bootstrap.yml`上配置

{% highlight java %}
## json setting
spring:
  #spring boot2.0.0 架构问题 时间处理 （映射，时区问题）
  jackson:
    #参数意义：
    #JsonInclude.Include.ALWAYS        默认
    #JsonInclude.Include.NON_DEFAULT   属性为默认值不序列化
    #JsonInclude.Include.NON_EMPTY     属性为 空（””） 或者为 NULL 都不序列化
    #JsonInclude.Include.NON_NULL      属性为NULL   不序列化
    #default-property-inclusion: ALWAYS
    date-format: yyyy-MM-dd HH:mm:ss
    #time-zone: GMT+8
    time-zone: Asia/Shanghai
    serialization:
      write-dates-as-timestamps: false
{% endhighlight %}

很遗憾，无效！

## 最终方案

**更改数据库连接参数**

`basic.mysql.url=jdbc:mysql:replication://x.x.x.x,x.x.x.x/xx?useSSL=false&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useLegacyDatetimeCode=false&serverTimezone=GMT+8`

顺利解决时间+8问题，插入查询时间正常！！！

## 问题产生原因

经过排查及查阅资料，出现这个问题并不是`SpringBoot2.x`的问题，而是`mysql`数据库链接jar包版本的问题。

由于升级了`SpringBoot2.x`，对应的`mysql-connector-java`版本从`5.1.36`升级到`8.0.15`.

版本升级后，查询出来的时间会比数据库存储的值多出6小时。

回想起最初发现这个问题的时候，时间差异是有14小时，如下：

{% highlight java %}

数据库存储的值：2019-06-13 15:22:03 

代码查询出来的值：2019-06-14 04:22:03

{% endhighlight %}

差异 = 8 + 6 = 14小时

数据库连接参数改为`serverTimezone=UTC`后，时间变成了：

{% highlight java %}

数据库存储的值：2019-06-13 15:22:03 

代码查询出来的值：2019-06-13 23:22:03

{% endhighlight %}

差异 = 8小时

最终数据库连接参数改为`serverTimezone=GMT+8`后，一切才恢复正常。

---

Ps：

其实最终就是要把数据库和程序的时区设置为一致，UTC也行，GMT+8也好，主要是保持一致。

以下举例将两者设置为UTC.

{% highlight java %} 
数据库连接参数： 
spring.datasource.url=jdbc:mysql://localhost:3306/db?useUnicode=true&characterEncoding=utf-8&useLegacyDatetimeCode=false&serverTimezone=UTC

其中useLegacyDatetimeCode参数默认是true，我们需要手动设置为false，否则无效。 

代码：
@SpringBootApplication
public class Application {
      @PostConstruct
      void started() {
            TimeZone.setDefault(TimeZone.getTimeZone("UTC"));
            //TimeZone.setDefault(TimeZone.getTimeZone("Asia/Shanghai"));
            //TimeZone.setDefault(TimeZone.getTimeZone("GMT+8"));
      } 
      public static void main(String[] args) { 
            SpringApplication.run(Application.class, args); 
      } 
}
{% endhighlight %}

这个方案我没亲自尝试过，不过应该也是可以解决问题的。

## 参考资料

- SpringBoot 统一时区的方案 [https://www.cnblogs.com/chancy/p/9995562.html](https://www.cnblogs.com/chancy/p/9995562.html)
- SpringBoot2.0设置时区问题 [https://www.liangzl.com/get-article-detail-27746.html](https://www.liangzl.com/get-article-detail-27746.html)
- JAVA插入数据到MySql少了8小时，多了6小时 [https://blog.csdn.net/u013042707/article/details/78783650](https://blog.csdn.net/u013042707/article/details/78783650)




