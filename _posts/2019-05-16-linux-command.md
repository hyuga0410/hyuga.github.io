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

## 查看系统版本
- `cat /etc/issue`
- `cat /etc/redhat-release`
- `cat /proc/version`

{% highlight java %}
CentOS release 6.1 (Final) 
Linux version 2.6.32-358.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-3) (GCC) ) #1 SMP Fri Feb 22 00:31:26 UTC 2013 
{% endhighlight %}

## 查看系统时间
- `date`

{% highlight java %}
2019年 07月 10日 星期三 14:43:34 CST
{% endhighlight %}

## 获取命令执行时间

`time [option] comand [arguments...]`

在命令执行完成之后就会打印出CPU的使用情况：
{% highlight java %}
real    0m5.064s      <== 实际使用时间（从command命令开始执行到运行终止的消耗的总时间）
user    0m0.020s      <== 用户态使用时间（命令执行完成花费的用户CPU时间，即命令在用户态中执行时间总和）
sys     0m0.040s      <== 内核态使用时间（命令执行完成花费的系统CPU时间，即命令在核心态中执行时间总和）
{% endhighlight %}

注：通常来说real > user+sys，比说说命令执行会等待IO，等待IO时并没有实际消耗CPU的时间。

## 安装wget命令

- debian 或者 ubuntu : `sudo apt-get install wget`
- centos : `sudo yum -y install wget`

## 重启命令
- `reboot` 普通重启
- `shutdown -r now` 立刻重启(root用户使用)
- `shutdown -r 10` 过10分钟自动重启(root用户使用)
- `shutdown -r 20:35` 在时间为20:35时候重启(root用户使用)

如果是通过shutdown命令设置重启的话，可以用shutdown -c命令取消重启

## 关机命令
- `halt` 立刻关机
- `poweroff` 立刻关机
- `shutdown -h now` 立刻关机(root用户使用)
- `shutdown -h 10` 10分钟后自动关机

如果是通过shutdown命令设置关机的话，可以用shutdown -c命令取消重启

## 命令编辑及光标移动

- `ctrl + c`（另起一行） 或 `ctrl + u`(撤销所有)：输入指令错误，想重新来过
- `ctrl + k`：删除光标后的文本
- `ctrl + a`：光标移动到命令开头
- `ctrl + e`：光标移动到命令结尾
- `ctrl + b`：光标向前移动一格
- `ctrl + f`：光标向后移动一格
- `ctrl + w`：删除一个段连续的文本（以空格隔开的字符串）

## 实时查看日志

`tail -f filename.log`

`tail -f -n 100 filename.log`

## 查看文件内容

`less filename.log`

## 查看进程运行时间

`ps -p (pid) -o lstart,etime`

```
$ ps -p 24525 -o lstart,etime 
                 STARTED     ELAPSED
Sat Mar 23 20:52:08 2019       02:45
```

其中24525是你要查看进程的进程id。

## 屏幕冻结

非常优秀的功能，tail -f 查看实时日志时，可以冻结屏幕查看日志

`ctrl + s` 冻结屏幕

`ctrl + q` 退出冻结

## 查看内容占用前10的进程

`$ ps -aux|sort -k4nr |head -n 10`

## 查看目录磁盘空间大小

`du -h --max-depth=1`

`--max-depth` 目录深度

## 显示各挂载点空间

`df -h`

## 显示内存可用情况

`free -h`

## find找到某一文件的目录

`find $PWD -name "server.xml"`

`$PWD` 目录，如/opt/

## 删除带某个字符串以外的文件

`rm -v !(*mts*)`

## 递归列出目录下的所有文件名

`ls -lR |grep '^-' |awk '{print "/LTE/"$9}'`

- `-R` 递归目录
- `^-` 文件（文件以-开头）
- `$9` 文件名

## 计算目录下的文件数量

`ls -lR |grep '^-'|wc -l`

## 文件编码格式转码

`iconv -f gb18030  -t utf8 1.txt -o 2.txt`

- gb18030是windows上文件的编码
- utf8是linux上文件的默认编码格式
- 1.txt是需要转换的文件
- 2.txt是转换后的结果

## 列出当前目录文件名

```
ls   #列出当前目录文件名，不包括隐藏文件，且无法看到符号链接链向的文件
# -a   ALL
ls -a #列出当前目录下所有文件，包括隐藏文件，当前目录.以及上一级目录..
ls -A #列出当前目录下所有文件，包括隐藏文件，不包括前目录.以及上一级目录..
ls -al  # 列出当前目录所有文件，并且使用长格式显示所有信息，包括权限，大小，用户，时间等，与ll作用相同
```

## 设置全局执行命令

linux将shell脚本所在目录路径添加到path，即可在系统任何地方执行该目录下的脚本，如果执行不了，请chmod授权。

步骤如下：

- `vim /etc/profile` 打开全局配置文件
- 添加到path，如下
- `source /etc/profile`使得配置生效，即可在任意地方执行脚本

{% highlight java %}
打开/etc/profile文件后，将as.sh脚本所在目录/data/www/hyuga添加到path后，如下

JAVA_HOME=/usr/local/jdk-11
PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin:$ANT_HOME/bin:/opt/node-v11.1.0-linux-x64/bin:/data/www/hyuga
MAVEN_HOME=/opt/maven/apache-maven-3.5.4
export ANT_HOME=/opt/ant/apache-ant-1.9.7
export JMETER=/opt/jmeter/apache-jmeter-5.0
export CLASSPATH=$JAVA_HOME/lib:$CONF_DIR$JMETER/lib/ext/ApacheJMeter_core.jar:$JMETER/lib/logkit-2.0.jar:$CLASSPATH
export JAVA_HOME PATH
{% endhighlight %}

## 设置全局快捷命令

`vim ~/.bash_profile`

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

## 以易读方式列出当前目录文件大小

`ls -lh`

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

## 查看系统运行时间

`uptime`

```
14:33  up 3 days,  3:46, 5 users, load averages: 4.42 4.24 4.11
```

## 查看系统已登录用户

`who -a`

```
hyuga0410 console  May 13 10:47
hyuga0410 ttys001  May 14 10:41
hyuga0410 ttys002  May 14 10:59
hyuga0410 ttys003  May 14 11:44
hyuga0410 ttys005  May 14 10:53
```

## 查看系统版本信息

`uname -a`

```
Linux lvs213.business 2.6.32-754.el6.x86_64 #1 SMP Tue Jun 19 21:26:04 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

操作系统版本为：lvs213.business，cpu类型为：x86_64

## 查看当前系统环境

`export`

## 查看IP地址

`ip addr`

## 查看网络连接状态

`netstat`

## 实时查看内存信息或网卡信息

```
watch -n 1 cat /proc/meminfo 
watch -n 1 cat /proc/net/dev
```
## lspci
- centos7中安装lspci工具 `yum install pciutils -y`
- 查看VGA显卡信息 `lspci | grep -i vga`

```shell script
00:02.0 VGA compatible controller: Cirrus Logic GD 5446
[root@lvs211 ~]# lspci -v -s 00:02.0
```
`00:02.0`表示显卡的代号，查看显卡详细信息 `lspci -v -s 00:02.0`
```shell script
00:02.0 VGA compatible controller: Cirrus Logic GD 5446 (prog-if 00 [VGA controller])
	Subsystem: Red Hat, Inc. QEMU Virtual Machine
	Physical Slot: 2
	Flags: fast devsel
	Memory at f0000000 (32-bit, prefetchable) [size=32M]
	Memory at f2000000 (32-bit, non-prefetchable) [size=4K]
	Expansion ROM at f2010000 [disabled] [size=64K]
	Kernel modules: cirrusfb
```

- 查看Nvidia(英伟达) GPU显卡信息 `lspci | grep -i nvidia`
- 查看Nvidia显卡信息及使用情况 `nvidia-smi`