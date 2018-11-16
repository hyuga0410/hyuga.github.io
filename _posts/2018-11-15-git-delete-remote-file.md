---
layout:       post
title:        "Git远程仓库文件删除"
subtitle:     "删除远程git仓库的文件方法"
date:         2018-11-15 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-26.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - git
---

# 前言
不小心把.idea下的文件或者.iml文件等commit到本地后，idea工具只能push，不能撤销。。。

照理说通过指令应该是可以将本地commit的文件进行撤销的，但是撤销怕是文件都会回滚，苦于个人能力有限，所以还是先push到远程，再通过指令把远程不需要的文件删除。

# 步骤
举个栗子：git删除远程.idea目录

- 进入项目路径，切换到对应的版本
- `git rm --cached -r .idea`
- `git commit -m "commit and remove .idea"`
- `git push origin master`（或者分支：git push origin dev_xxxx）


# 常规命令
- git config --global user.name "[username]"
- git config --global user.email "[email]"
- git config --global http.proxy [proxy url:port]
- 查看git配置列表：git config --list
- git status