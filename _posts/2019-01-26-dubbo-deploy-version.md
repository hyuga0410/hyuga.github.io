---
layout:       post
title:        "Dubbo服务配置动态版本号"
subtitle:     "服务启动时根据配置信息设置版本号"
date:         2019-01-31 15:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-31.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - dubbo
---

# 前言
有时为了在不同的环境进行联调，需要给dubbo服务加上version、group或者url。

但是环境切换的时候，就显得很繁琐，需要不停的改版本号，重打jar包，替换，启动。

所以是否能把版本号设置到配置文件中，当服务启动时，自动从配置文件中获取对应的版本号赋值启动。

`${xxx.dubbo.version}`

# 步骤
> 配置文件配好

![](/img/2019/2019-01/dubbo-version-1.png)

> 具体代码

springboot注解注入方式

![](/img/2019/2019-01/dubbo-version-3.png)

xml配置文件注入方式

![](/img/2019/2019-01/dubbo-version-2.png)

> 注意

一图中的syzl.dubbo.yfb.version不能删除，只能是`syzl.dubbo.yfb.version=`

如果删除，会导致服务启动失败，因为找不到这个配置的key。

这样的就完成了配置版本号功能，切换不同的环境不再需要重复改版本号打jar包，只需要替换不同环境的配置文件，重启服务即可。

同样的，也可以配置group或者url。
