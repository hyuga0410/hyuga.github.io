---
layout:       post
title:        "Git远程仓库地址变更，挽救本地代码"
subtitle:     "Git远程仓库更换地址后，修正本地仓库配置"
date:         2018-11-05 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-23.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - git
---

# 前言
Git远程仓库更改路径后，导致本地项目对应不上，本地代码无法同步、更新、提交。

如果重新checkout项目，再一个个文件对应迁移，无疑浪费生命......

其实，只需要更新本地项目的git地址即可完成同步操作！！！

# 步骤
- 终端进入项目路径，执行git remote -v

![](/img/2018/2018-11/git-1.png)

![](/img/2018/2018-11/git-2.png)

- 这时会发现origin对应的地址不是新的项目git地址，执行git remote remove origin，移除项目git地址配置

- 添加新的地址git remote add origin http://git.xxx.com/xxxx/xxxx-xxxx-services.git

- 执行git fetch 

- 执行 git branch --set-upstream-to=origin/master master

- 执行git pull

好了，就这么简单，这是更新本地主分支地址的方法，记录下以备不时之需。

# 常规命令
- git config --global user.name "[username]"
- git config --global user.email "[email]"
- git config --global http.proxy [proxy url:port]
- 查看git配置列表：git config --list
- git status