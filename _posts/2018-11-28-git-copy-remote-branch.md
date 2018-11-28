---
layout:       post
title:        "Git拷贝分支并推送到远程服务器"
subtitle:     "拷贝本地分支，推送到git服务器"
date:         2018-11-28 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-27.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - git
---

# 前言
记录如何拷贝一个本地分支，并将分支推送到git服务器，并建立新分支在本地与服务器之间的关联，保证能正常更新。

# 步骤

- 进入项目路径，切换到对应的版本
- `git pull`
- `git checkout -b test_xxx dev_xxx`[test是新分支、dev是原分支]
- `git push origin test_v1.0.0_garden_modeling:test_v1.0.0_garden_modeling`[本地分支与git远程分支同名]

到这一步已经将本地新建好的分支推送到git服务器了，但是这个时候git pull失败，提示大致如下：
![](/img/2018/2018-11/git-3.png)

原因：git不知道要将这个分支要pull远程的哪个分支。

这里有两种解决方法：

**方案一**

直接指定是从哪个远程分支pull

```
git pull <remote name> <remote branch name>
```

不过这种较为麻烦，建议使用方案二

**方案二**

建立本地分支与远程分支的联系，这样每次git pull都不需要特定指明是从哪个远程分支更新。

```
git branch --set-upstream-to=<remote name>/<branch name> <local branch name>

例如绑定主线master的联系:
git branch --set-upstream-to=origin/master master

例如绑定分支test的联系：
git branch --set-upstream-to=origin/test_v1.0.0_garden_modeling test_v1.0.0_garden_modeling
```

# 常规命令
- git config --global user.name "[username]"
- git config --global user.email "[email]"
- git config --global http.proxy [proxy url:port]
- 查看git配置列表：git config --list
- git status
- git branch：查看所有本地分支
- git branch -a：查看所有远程和本地分支