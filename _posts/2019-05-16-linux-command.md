---
layout:       post
title:        "Linux 常规指令技巧"
subtitle:     "Linux 平时使用的一些指令小技巧"
date:         2019-05-16 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-8.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - linux
---

## 前言

**如果你对以下内容感兴趣，请前往大神[【守望的个人博客】](https://www.yanbinghu.com/)**

## 命令编辑及光标移动

- `ctrl + c`（另起一行） 或 `ctrl + u`(撤销所有)：输入指令错误，想重新来过
- `ctrl + k`：删除光标后的文本
- `ctrl + a`：光标移动到命令开头
- `ctrl + e`：光标移动到命令结尾
- `ctrl + b`：光标向前移动一格
- `ctrl + f`：光标向后移动一格
- `ctrl + w`：删除一个段连续的文本（以空格隔开的字符串）

## 日志

> 实时查看日志

- `tail -f filename.log`
- `tail -f -n 100 filename.log`

> 查看文件内容

- `less filename.log`

# 查看进程运行时间

- `ps -p (pid) -o lstart,etime`

```
$ ps -p 24525 -o lstart,etime 
                 STARTED     ELAPSED
Sat Mar 23 20:52:08 2019       02:45
```

其中24525是你要查看进程的进程id。

# 屏幕冻结

> 非常优秀的功能，tail -f 查看实时日志时，可以冻结屏幕查看日志

- `ctrl + s` 冻结屏幕
- `ctrl + q` 退出冻结

# 查看内容占用前10的进程
- `$ ps -aux|sort -k4nr |head -n 10`

# 查看目录磁盘空间大小

- `du -h --max-depth=1`

`--max-depth` 目录深度

# 显示各挂载点空间

- `df -h`

# 显示内存可用情况

- `free -h`

# find找到某一文件的目录

- `find $PWD -name "server.xml"`

`$PWD` 目录，如/opt/

# 删除带某个字符串以外的文件

- `rm -v !(*mts*)`

# 递归列出目录下的所有文件名

- `ls -lR |grep '^-' |awk '{print "/LTE/"$9}'`

    - `-R` 递归目录
    - `^-` 文件（文件以-开头）
    - `$9` 文件名

# 计算目录下的文件数量

- `ls -lR |grep '^-'|wc -l`

# 文件编码格式转码

- `iconv -f gb18030  -t utf8 1.txt -o 2.txt`

    - gb18030是windows上文件的编码
    - utf8是linux上文件的默认编码格式
    - 1.txt是需要转换的文件
    - 2.txt是转换后的结果

# ls

> 列出当前目录文件名

```
ls   #列出当前目录文件名，不包括隐藏文件，且无法看到符号链接链向的文件
# -a   ALL
ls -a #列出当前目录下所有文件，包括隐藏文件，当前目录.以及上一级目录..
ls -A #列出当前目录下所有文件，包括隐藏文件，不包括前目录.以及上一级目录..
ls -al  # 列出当前目录所有文件，并且使用长格式显示所有信息，包括权限，大小，用户，时间等，与ll作用相同
```

> 设置全局快捷命令

- `vim ~/.bash_profile`

设置。。。

```
 export M2_HOME=/usr/local/Cellar/maven/3.6.0
 export PATH=$PATH:$M2_HOME/bin

 alias ll='ls  -la'

 #enables colorin the terminal bash shell export
 export CLICOLOR=1

 #setsup thecolor scheme for list export
 export LSCOLORS=gxfxcxdxbxegedabagacad

 #sets up theprompt color (currently a green similar to linux terminal)
 export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;36m\]\w\[\033[00m\]\$ --> '
 #enables colorfor iTerm
 export TERM=xterm-256color

 [[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*

 export JAVA_7_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home

 export JAVA_8_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home

 export JAVA_10_HOME=/Library/Java/JavaVirtualMachines/jdk-10.jdk/Contents/Home
 export JAVA_HOME=$JAVA_8_HOME

 alias jdk8='export JAVA_HOME=$JAVA_8_HOME'
 alias jdk7='export JAVA_HOME=$JAVA_7_HOME'
 alias jdk10='export JAVA_HOME=$JAVA_10_HOME'
```

> 以易读方式列出当前目录文件大小

- `ls -lh`

文件大小不以初始字节显示，而是以k或者M为单位显示。

```
ls -lh   
总用量 1.4M
drwxrwxr-x 3 hyb  hyb  4.0K 10月 19  2017 Area3
drwxrwxr-x 3 hyb  hyb  4.0K 10月 19  2017 home
-rw-r--r-- 1 root root 1.3K 10月 19  2017 home.zip
lrwxrwxrwx 1 hyb  hyb     8 9月  13 21:19 test -> home.zip
-rw-rw-r-- 1 hyb  hyb  1.3M 9月  16 15:30 test.zip
drwxrwxr-x 2 hyb  hyb  4.0K 10月 19  2017 user
```

## 系统状态

> 查看系统运行时间

- `uptime`

```
14:33  up 3 days,  3:46, 5 users, load averages: 4.42 4.24 4.11
```

> 查看系统已登录用户

- `who -a`

```
hyuga0410 console  May 13 10:47
hyuga0410 ttys001  May 14 10:41
hyuga0410 ttys002  May 14 10:59
hyuga0410 ttys003  May 14 11:44
hyuga0410 ttys005  May 14 10:53
```

> 查看系统版本信息

- `uname -a`

```
Linux lvs213.business 2.6.32-754.el6.x86_64 #1 SMP Tue Jun 19 21:26:04 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

操作系统版本为：lvs213.business，cpu类型为：x86_64

> 查看当前系统环境

- `export`

> 查看IP地址

- `ip addr`

> 查看网络连接状态

- `netstat`

> 实时查看内存信息或网卡信息

```
watch -n 1 cat /proc/meminfo 
watch -n 1 cat /proc/net/dev
```