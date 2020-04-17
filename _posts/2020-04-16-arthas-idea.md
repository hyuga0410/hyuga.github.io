---
layout:       post
title:        "阿里巴巴-Arthas诊断工具-IDEA工具"
subtitle:     "Alibaba开源的Java诊断工具-Arthas-IDEA工具"
date:         2020-04-16 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-24.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - Arthas
---

## 官方简介

`Arthas`（阿尔萨斯）是阿里巴巴开源的 Java 诊断工具

官方文档：[https://alibaba.github.io/arthas](https://alibaba.github.io/arthas)

项目地址：[https://gitee.com/arthas/arthas](https://gitee.com/arthas/arthas)

PS: 
- 大致内容之前已整理 [【阿里巴巴-Arthas诊断工具】](http://hyuga.top/2019/07/30/arthas/)
- 本文主要讲解Arthas的idea插件，以及arthas的简单用法。

## Arthas-IDEA

最近出了Arthas的idea插件，简单用下，也简单记录下。

**事先说明，IDEA插件主要用于便捷生成arthas命令**

**事先说明，IDEA插件主要用于便捷生成arthas命令**

**事先说明，IDEA插件主要用于便捷生成arthas命令**

#### 简易安装

打开Idea，搜索Arahas插件，安装，方便。

本地或服务器上，创建arthas目录，`cd arthas`，执行`curl -L https://alibaba.github.io/arthas/install.sh | sh`

![](/img/2020/2020-04/arthas-idea-1.png)

> 启动arthas.sh

`sh arthas.sh`

![](/img/2020/2020-04/arthas-idea-2.png)

选择展示出来的进程pid对应的序号，enter，好，接下来正式进入arthas诊断。

![](/img/2020/2020-04/arthas-idea-3.png)

#### Arthas-IDEA插件支持功能

![](/img/2020/2020-04/arthas-idea-4.png)

![](/img/2020/2020-04/arthas-idea-4.1.png)

支持的功能主要有：

![](/img/2020/2020-04/arthas-idea-5.png)

![](/img/2020/2020-04/arthas-idea-0.png)

[【官网插件地址】](https://github.com/WangJi92/arthas-idea-plugin)

## IDEA结合诊断工具使用

#### install

选中方法，点击`Arthas Command`，选择`Install`，会自动生成 arthas 安装命令并复制到剪贴板。

`curl -sk https://arthas.gitee.io/arthas-boot.jar -o ~/.arthas-boot.jar  && echo "alias as.sh='java -jar ~/.arthas-boot.jar --repo-mirror aliyun --use-http'" >> ~/.bashrc && source ~/.bashrc`

#### watch

**查看异常日志、入参、出参**

选中方法，点击`Arthas Command`，选择`Watch`，会自动生成watch命令并复制到剪贴板。

进入arthas诊断控制台，黏贴：`watch com.hyuga.dict.controller.ApiGardenController getGardenDetails '{params,returnObj,throwExp}' -n 5 -x 3`

接下来如果有请求调用该接口，则会在控制台打印出入参、出参、cost等信息。

但因信息过程，为了不被后续请求干扰当次请求信息查看，可以`Press Q or Ctrl+C to abort.`按`Q`或者`Ctrl+C`退出当前命令。

![](/img/2020/2020-04/arthas-idea-6.png)

#### trace

**查看调用链（是否调用方法）、耗时性能排查**

`trace com.hyuga.dict.controller.ApiGardenController getGardenDetails -n 5`

![](/img/2020/2020-04/arthas-idea-7.png)

#### stack

**获取方法从哪里执行的调用栈（用途：源码学习调用堆栈，了解调用流程）**

`stack com.hyuga.dict.controller.ApiGardenController getGardenDetails -n 5`

![](/img/2020/2020-04/arthas-idea-8.png)

#### monitor

**方法执行监控（性能问题排查，一段时间内的性能指标）**

`monitor com.hyuga.dict.controller.ApiGardenController getGardenDetails -n 10 --cycle 10` 每10s统计一次

![](/img/2020/2020-04/arthas-idea-9.png)

## 进阶用法

#### 获取classloader命令

`sc -d com.hyuga.dict.utils.StringUtil`

最后一行即可获取到类加载器

#### ognl

- Invoke Static Method Field（调用静态方法字段）
- Set Static Field（设置静态字段）
- Get Selected Spring Property（获取选定的Spring属性）
- Get All Spring Property（获取所有Spring属性）

一、Invoke Static Method Field（调用静态方法字段）

`ognl  -x  3 '@com.hyuga.dict.model.exception.ParameterIllegalException@serialVersionUID' -c 1bce4f0a`

调用查看该静态方法字段的值

二、Set Static Field（设置静态字段）
![](/img/2020/2020-04/arthas-idea-10.png)
![](/img/2020/2020-04/arthas-idea-11.png)

a. 选中常量`private static final String BASE_PACKAGE = "hyuga";`

b. 先点击`获取classloader命令`到控制台执行，看到最后一行`classLoaderHash   1bce4f0a`

c. 把`1bce4f0a`年调到 -c classloader 的输入框中，赋值的地方，填入`hyuga-test`，再点击`获取Command`

d. 得到`ognl -x 3  '#field=@com.hyuga.dict.config.AutoMapConfig@class.getDeclaredField("BASE_PACKAGE"),#modifiers=#field.getClass().getDeclaredField("modifiers"),#modifiers.setAccessible(true),#modifiers.setInt(#field,#field.getModifiers() & ~@java.lang.reflect.Modifier@FINAL),#field.setAccessible(true),#field.set(null,"hyuga-test")' -c 1bce4f0a`

e. 控制台执行，则运行的服务中该静态变量变更为`hyuga-test`

三、Get Selected Spring Property（获取选定的Spring属性）

便于查询某个配置项是否生效。

![](/img/2020/2020-04/arthas-idea-14.png)

要先设置 applicationContextProvider，具体参考官方文档。

ognl -x 3 '#springContext=@applicationContextProvider@context,#springContext.getEnvironment().getProperty("BASE_PACKAGE")' -c 1bce4f0a

四、Get All Spring Property（获取所有Spring属性）

#### Time Tunnel

**方法执行数据的时空隧道，记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测(可以重新触发，周期触发，唯一缺点对于ThreadLocal 信息丢失[隐含参数]、引用对象数据变更无效)**

`tt -t com.hyuga.dict.controller.ApiGardenController getGardenDetails -n 5`

![](/img/2020/2020-04/arthas-idea-12.png)

INDEX 代表当次请求

- `tt -p -i 1000` 重新触发一次（打印请求和响应等详细信息）
![](/img/2020/2020-04/arthas-idea-13.png)

- `tt -w '{method.name,params,returnObj,throwExp}' -x 3 -i 1000`（打印入参he返回值）

- `tt -p --replay-times 3 --replay-interval 2000 -i 1000`（周期性触发三次）

> 全部命令
- 执行一次TT列表中的1000 `tt -p -i 1000  `
- 查看第一个参数 `tt  -w params[0] -i 1000`
- 查看方法执行参数 `tt  -w '{method.name,params,returnObj,throwExp}' -x 3 -i 1000`
- 周期性执行 `tt -p --replay-times 3 --replay-interval 2000 -i 1000`
- 时光隧道列表 `tt -l`
- 删除时光隧道列表 `tt --delete-all`

## PS
未完待续