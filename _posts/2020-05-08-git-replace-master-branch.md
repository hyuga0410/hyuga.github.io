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
    - git
---

# 前言
`master`分支之前因为一些莫名的原因，再去合并线上分支会一直冲突。某种意义上来说，线上分支即`master`分支，所以，想删掉旧的`master`分支，用线上分支替代`master`分支。

# 操作

**操作很简单，就3条命令**

- git branch -m master old-master
    - 删除本地`master`分支
- git branch -m wait_change_master master
    - 把本地新分支`wait_change_master`重命名为`master`
- git push -f origin master
    - 将新的`master`分支强制推送到git仓库

# PS
感谢原作者分享！！！

# 内容来自
版权声明：本文为CSDN博主「github.com/andarm」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ljymoonlight/article/details/93967238