---
layout:       post
title:        "阿里云Code git clone ssh方式"
subtitle:     "阿里云Code git clone 采坑记"
date:         2019-03-18 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-15.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - aliyun
---

# 前言
最近迁移项目源码至阿里云code，遇到一些问题，下面记录下。

# 阿里云CODE
代码迁移完后，配合jenkins的构建平台，需要一个通用账号用于拉取源码，这个时候就需要一个手机号注册一个空账号，用于源码拉取构建。

问题是，哪来那么多的手机号。

所以，放弃`git clone https://xxxxxxxx`原先的这种方案。

改用`git clone ssh://xxxxxxxxx`方案。

![](/img/2019/2019-03/aliyun-problem-1.png)

打开本机终端，执行`cat ~/.ssh/id_rsa.pub`得到如下代码段。

![](/img/2019/2019-03/aliyun-problem-3.png)

录入到阿里云的SSH keys中。

![](/img/2019/2019-03/aliyun-problem-2.png)

这时，就可以在本地终端中拉取源码`git clone -b dev_v1.0.xxx https://code.aliyun.com/xxx-xxx/xxx-xxx-xxx.git`

本地测试没有任何问题，一步到位。

# 问题记录

问题出现在测试环境。

测试环境配置完后，拉取源码的时候报链接超时。

经过检查发现本地拉取，是通过22端口，而测试环境拉取是通过9988端口。

`ssh -vT git@git.coding.net`

提示连接端口是9988，而本地连接端口是22.


{% highlight java %}
[root@238 test]# git clone  git@code.aliyun.com:syzl-business/syzl-micro-services.git
Initialized empty Git repository in /data/www/test/syzl-micro-services/.git/

ssh: connect to host code.aliyun.com port 9988: Connection timed out
fatal: The remote end hung up unexpectedly
{% endhighlight %}

折腾了很久也没明白为什么测试环境会通过9988这个端口调用阿里云，这个等后面再去研究了，时间比较紧，就先解决当前的情况。

# 解决方案

最终采取的方案是指定22端口！！！

`git clone -b dev_v1.0.0_xxx ssh://git@code.aliyun.com:22/xxx-xxx/xxx-xxx-xxx.git`


