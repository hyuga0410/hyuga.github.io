---
layout:       post
title:        "Git替换master分支并推送到远程服务器"
subtitle:     "删除远程master分支，本地新分支改名为master，推送到git服务器"
date:         2020-05-08 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-26.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - es
---

## 前言
公司最近搭建了`es`和`es-head`，`es-head`控制台每次打开，连接地址框每次都是默认的`http://localhost:9200`，每次都要自己手动去改这个地址再点连接，甚是麻烦。

经查阅，找到较为简单的解决方案。

## 步骤

进入服务器

进入es-head安装目录 `cd /opt/elasticsearch-head`

`vim _site/app.js`

找到这段代码

`this.base_uri = this.config.base_uri || this.prefs.get("app-base_uri") || "http://localhost:9200"`

改成你要的地址，如：

`this.base_uri = this.config.base_uri || this.prefs.get("app-base_uri") || "http://10.152.2.212:9200"`

接下来重启es-head即可。

这样进入页面，默认就是 `http://10.152.2.212:9200`

![](/img/2020/2020-05/es-head-1.png)

## 重启es-head

重启`es-head`这里有点坑，没找到restart相关的命令。

粗暴处理如下：

根据端口找到进程id  `netstat -tunlp | grep 9100`

干掉进程  `kill -9 pid`

重启服务  `grunt server`
