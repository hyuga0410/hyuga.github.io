---
layout:       post
title:        "MySQL - 基础架构"
subtitle:     "一条语句的一生经历"
date:         2018-11-24 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-27.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - mysql
---

## mysql基本架构
mysql基本架构主要分两部分：

- Server层（一个）
    - 连接器：管理连接，权限验证
    - 查询缓存：命中则直接返回结果
    - 分析器：词法分析，语法分析
    - 优化器：执行计划生成，索引选择
    - 执行器：操作引擎，返回结果
- 存储引擎（多个）

【多个存储引擎共用一个Server层】

Server层还涵盖 MySQL 的大多数核心服务功能，以及所有的内置函数（如日期、时间、数学和加密函数等），所有跨存储引擎的功能都在这一层实现，比如存储过程、触发器、视图等。

存储引起主要负责数据的存储和提取。其架构模式是插件式的，支持InnoDB、MyISAM、Memory等多个存储引擎。

自MySQL5.5.5版本开始，InnoDB已经成为了默认的存储引擎。

## 一条sql的一生经历

举例：select * from t_user where fid = 1;

首先，客户端要连接到数据库，这个时候会和连接器打交道，连接器主要负责跟客户端建立连接、获取权限、维持和管理连接。

`mysql -h$ip -P$port -u$user -p`

连接过程，连接器完成tcp握手后，会验证你的用户名和密码，失败拒绝连接，成功则获取用户权限，用于判断用户后续执行语句是否有相应权限。







