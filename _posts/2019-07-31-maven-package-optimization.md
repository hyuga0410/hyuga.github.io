---
layout:       post
title:        "Maven打包优化方案"
subtitle:     "Maven Package Optimization"
date:         2019-07-31 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-17.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - maven
---

## 前言

测试环境采用`jenkins`+`maven`+`nexus`打包，随着项目慢慢变大，打包越来越慢，难以忍受每次打包要好几分钟。

当真是改bug 1分钟，打包10分钟。

为此，萌生了打包优化提速的想法，经过查找资料和分析，采用以下方案。

## 方案

由于每次打包都是删除服务器的源码文件，再重新拉取新的源码，所以第一步先移除`clean`，直接`mvn install`.

接着跳过单元测试，`-D maven.test.skip=true`

再采用多线程打包方案，`-Dmaven.compile.fork=true`

如果用的Maven是3.×以上版本，可以增加 -T 1C 参数，表示每个CPU核心跑一个工程。

有点困惑的地方在于`-U`，是否需要每次打包都去检查`snapshot`更新检查。这块我不是很确定必要性。 照理说`releases`是不需要每次都去检查更新的，快照包才需要。 只是这个要看情况，如果项目中快照包是可控的情况下，是可以考虑不用`-U`. 

最终打包脚本如下：

{% highlight shell %}
mvn install -Dmaven.test.skip=true -Dmaven.compile.fork=true -T 1C
{% endhighlight %}
