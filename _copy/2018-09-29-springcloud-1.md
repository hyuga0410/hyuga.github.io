---
layout:       post
title:        "Spring Cloud简介"
subtitle:     ""
date:         2018-09-11 22:42:05
author:       "Hyuga"
header-img:   "img/2018/2018-09/head-top-4.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - cloud
---

Spring Cloud中文官网：https://springcloud.cc/

- Spring Cloud Config
    - Spring 配置管理工具包，让你可以把配置放到远程服务器，集中化管理集群配置，目前支持本地存储、Git以及Subversion。
- Eureka
    - Netflix 云端服务发现，一个基于 REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移。
- Hystrix
    - Netflix 熔断器，容错管理工具，旨在通过熔断机制控制服务和第三方库的节点,从而对延迟和故障提供更强大的容错能力。
- Feign
    - OpenFeign Feign是一种声明式、模板化的HTTP客户端。