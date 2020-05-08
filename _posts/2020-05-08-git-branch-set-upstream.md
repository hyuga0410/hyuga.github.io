---
layout:       post
title:        "GIT本地关联远程分支"
subtitle:     "git branch --set-upstream 本地关联远程分支"
date:         2020-05-08 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-34.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - git
---

## 原文链接
作者：iOS开发到汽修
链接：[https://www.jianshu.com/p/403d6ad11e30](https://www.jianshu.com/p/403d6ad11e30)
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 原文内容
最近使用git pull的时候多次碰见下面的情况：

{% highlight java %}
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

git branch --set-upstream-to=origin/<branch> release
{% endhighlight %}

其实，输出的提示信息说的还是比较明白的。

使用git在本地新建一个分支后，需要做远程分支关联。如果没有关联，git会在下面的操作中提示你显示的添加关联。

关联目的是在执行git pull, git push操作时就不需要指定对应的远程分支，你只要没有显示指定，git pull的时候，就会提示你。

解决方法就是按照提示添加一下呗：

`git branch --set-upstream-to=origin/remote_branch  your_branch`

其中，origin/remote_branch是你本地分支对应的远程分支；your_branch是你当前的本地分支。
