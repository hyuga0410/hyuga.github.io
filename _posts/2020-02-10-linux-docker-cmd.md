---
layout:       post
title:        "Linux Docker一些命令"
subtitle:     "Linux Docker相关一些常用命令"
date:         2020-02-10 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-18.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - mac
---

## 参考资料

教程内容来自：[进入容器](https://yeasy.gitbooks.io/docker_practice/container/attach_exec.html)

大佬的GitHub：[yeasy/docker_practice](https://github.com/yeasy/docker_practice)

## 前言
测试环境solr部署在docker中，无法直接查看日志信息，需要进入容器内部。

## 步骤

- 查看容器列表 `docker container ls`
- 进入容器 `docker exec -it 69d1xxxx bash`

使用`docker attach 69d1xxxx`命令也可以进入容器，但不同的是：

- 使用`attach`的话，如果从这个 stdin 中 exit，会导致容器的停止。
- 使用`exec`，则退出不会导致容器停止。


