---
layout:       post
title:        "Hosts-Switch-Alfred插件"
subtitle:     "原文：https://mrdear.cn/posts/tools-hosts.html"
date:         2020-05-14 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-37.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - 工具
---

插件来源:[【造轮子-- Hosts-Switch-Alfred插件】](https://mrdear.cn/posts/tools-hosts.html)

> **感谢作者[屈定](https://mrdear.cn/)的分享**

## 引用

本人遇到的问题和屈定大佬遇到的一样，这里引用原作者的一段开场白。

> 最近遇到了多环境的问题，同一个域名在线下，预发，线上分别访问的是不同的地址，因此有了hosts便捷更换的需求。对于这种工具我不是很想运行一个独立后台的软件，因此一开始就把`IHost`，`SwitchHosts`类似的独立软件给排除掉了，调研到最后发现还是利用Alfred最能满足我的需求。

很久没有逛屈定大佬的博客了，没想到这么碰巧的发现了想要的东西，666

简单点说，就是配置多份`hosts`文件，通过`Alfred`工具进行快捷切换。不用再像之前一样用第三方工具（如：`iHosts`）要点下切换，然后再到终端贴下授权命令，再输入密码，相当繁琐。

亲测hosts切换后，生效比原iHosts还快，简直起飞。。。

## 用法

怎么用大佬的博客已经写得很清楚了，里面还有插件的下载链接，这里就不啰嗦了。

![](/img/2020/2020-05/tools-hosts-1.png)
![](/img/2020/2020-05/tools-hosts-2.png)
![](/img/2020/2020-05/tools-hosts-3.png)
![](/img/2020/2020-05/tools-hosts-4.png)
![](/img/2020/2020-05/tools-hosts-5.png)

最后一步通过Alfred选中对应的文件的时候，按住`Command` + `Enter`，可以使用默认编辑器打开hosts文件进行修改。

## PS
唯一有点不适应的是，原先第三方的是有个默认的配置，里面包含了一些通用的，不随其他环境变更的，每次勾选都类似于：默认+205这样。

现在就是得把这部分默认的映射拷贝到各个hosts配置中。

不过对于个人而言，现在通过`Alfred`来切换hosts，比之前方便很多，也不用再去运行一个`iHosts`软件，不用再每次切换去输一遍密码。


