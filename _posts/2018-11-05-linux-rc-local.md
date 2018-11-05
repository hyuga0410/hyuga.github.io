---
layout:       post
title:        "Linux启动或重启时执行命令与脚本"
subtitle:     "Linux启动或重启时执行自定义shell脚本"
date:         2018-11-05 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-24.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - linux
---

# 前言
测试环境或线上环境服务器重启的时候，每次都要重新手动运行某些命令或者脚本，太费时费力，而且时间久了可能自己都忘了当初脚本放在哪个路径下。。。

下面将介绍两种设定服务器重启时自动执行自定义脚本的方法。

# 使用rc.local

linux系统启动或重启时，会执行`/etc/rc.local`，我们只需要将服务的启动脚本写入即可，等下次服务器重启的时候，就可以坐下来淡定的喝茶了。

**步骤如下：**
- sudo chmod +x /etc/rc.local  如果你不是root账号，请执行
- sudo vi /etc/rc.local
- 写入启动脚本（注意执行命令必须全路径）
- :wq 保存退出，搞定！

![](/img/2018/2018-11/linux-1.png)

如果是CentOS，则修改的文件是`/etc/rc.d/rc.local` 而不是 `/etc/rc.local`.

# 使用Crontab

这个方法比较简单，在crontab任务管理中新建一个cron任务。

**步骤如下：**
- crontab -l 列出当前用户的所有crontab任务
- crontab -e 编辑当前用户所有的crontab任务
- @reboot ( sleep 90 ; sh \location\script.sh ) 【script.sh脚本是你自定义的脚本文件，系统启动90s后执行】

当然也可以添加定时任务

每周五9点30打开周报网址！！！
{% highlight shell %}
30 9 * * 5 osascript -e 'display notification "submit weekly,please! " with title "weekly remind"'  && (open 'https://app.huoban.com/xxx') || say no
{% endhighlight %}


