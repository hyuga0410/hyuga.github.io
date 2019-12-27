---
layout:       post
title:        "SpringBoot + Schedule"
subtitle:     "SpringBoot + Schedule 实现简易定时任务"
date:         2019-12-28 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-16.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - SpringBoot
---

本文内容转自：
[简书：【SpringBoot + Schedule 实现定时任务】作者：luokaiii](https://www.jianshu.com/p/4d9c9b08111d)

#### 前言

常规项目还是建议使用`Quartz` + `MQ`作为定时调度机制。

小项目或特殊项目，可采用`@Scheduled`，且`SpringBoot`默认支持Schedule，甚是便捷。

#### 用法

1. 启动类开启调度`@EnableScheduling`
{% highlight java %}
@SpringBootApplication
@EnableScheduling
public class Application{
    public static void mian(String[] args){
        SpringApplication.run(Application.class,args);
    }
}
{% endhighlight %}

2. 间隔执行
{% highlight java %}
@Scheduled(fixedRate = 5000) //表示 每隔 5000 毫秒执行一次 
public void reportCurrentTime() { 
    System.out.println("每隔五秒钟执行一次：" + dateFormat.format(new Date())); 
} 
{% endhighlight %}

3. 定时执行
{% highlight java %}
@Scheduled(cron = "0 30 11 ? * *") // 表示 在指定时间执行 
public void fixTimeExecution() { 
    System.out.println("在指定时间 " + dateFormat.format(new Date()) + "执行"); 
}
{% endhighlight %}

#### 参数说明
{% highlight java %}
* 第一位，表示秒，取值 0-59
* 第二位，表示分，取值 0-59
* 第三位，表示小时，取值 0-23
* 第四位，日期，取值 1-31
* 第五位，月份，取值 1-12
* 第六位，星期几，取值 1-7
* 第七位，年份，可以留空，取值 1970-2099

(*) 星号：可以理解为“每”的意思，每秒、没分
(?) 问好：只能出现在日期和星期这两个位置，表示这个位置的值不确定
(-) 表达一个范围，如在小时字段中使用 10-12 ，表示从10点到12点
(,) 逗号，表达一个列表值，如在星期字段中使用 1,2,4 ，则表示星期一、星期二、星期四
(/) 斜杠，如 x/y ，x是开始值，y是步长，如在第一位(秒)使用 0/15，表示从0秒开始，每15秒

官方解释：
0 0 3 * * ?         每天 3 点执行
0 5 3 * * ?         每天 3 点 5 分执行
0 5 3 ? * *         每天 3 点 5 分执行
0 5/10 3 * * ?      每天 3 点 5 分，15 分，25 分，35 分，45 分，55 分这几个点执行
0 10 3 ? * 1        每周星期天的 3 点10 分执行，注：1 表示星期天
0 10 3 ? * 1#3      每个月的第三个星期的星期天 执行，#号只能出现在星期的位置

注：第六位(星期几)中的数字可能表达不太正确，可以使用英文缩写来表示，如：Sun
{% endhighlight %}

