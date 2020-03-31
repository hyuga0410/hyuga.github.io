---
layout:       post
title:        "Oracle空格处理"
subtitle:     "Oracle空格处理函数"
date:         2020-03-31 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-20.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - oracle
---

update t_table c set c.fname = replace(c.fname,chr(9),')//去掉tab符号的

update t_table c set c.fname = replace(c.fname,chr(10),')//去掉回车符号的

update t_table c set c.fname = trim(c.fname)//去掉两边空格符号的
update t_table c set c.fname = ltrim(c.fname)//去掉左边空格符号的
update t_table c set c.fname = rtrim(c.fname)//去掉右边空格符号的