---
layout:       post
title:        "Docker安装过程记录"
subtitle:     "学习docker安装使用过程记录"
date:         2018-08-23 22:42:05
author:       "Hyuga"
header-img:   "img/2018-08-23/head-top.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - docker
---

初次安装使用Docker，记录下步骤和遇到的问题

本文使用操作系统：`MAC`

# 下载Docker
开始通过指令下载：
```
brew cask install docker
```
虽然开了`shadwsocks`，但是一直下载不了，最后还是直接http下载：
[https://download.docker.com/mac/stable/26399/Docker.dmg](https://download.docker.com/mac/stable/26399/Docker.dmg)

# 安装
先到官网注册账号：[https://cloud.docker.com/?next=https%3A%2F%2Fcloud.docker.com](https://cloud.docker.com/?next=https%3A%2F%2Fcloud.docker.com)

![](/img/2018-08-23/1.png)
拖拽安装，搞定！！！
![](/img/2018-08-23/2.png)
登录！！！
因为我之前登录过了，所以是这个界面。

# 检测是否成功
```
$ docker --version
Docker version 17.10.0-ce, build f4ffd25
$ docker-compose --version
docker-compose version 1.17.0-rc1, build a0f95af
$ docker-machine --version
docker-machine version 0.13.0, build 9ba6da9
```

OK! 正常安装.

# 配置国内镜像加速
![](/img/2018-08-23/3.png)
![](/img/2018-08-23/4.png)
点击图标 -> Preferences... -> Daemon -> Registry mirrors
- 网易加速：http://hub-mirror.c.163.com
- Docker 官方提供的中国 registry mirror：https://registry.docker-cn.com
- 七牛云加速器：https://reg-mirror.qiniu.com/
> 选一个配置，如果拉取不到镜像，就换一个加速地址配置
>
> 建议使用官方提供

# 安装过程遇到问题
* 提示本机已安装过`VirtualBox`，请升级或者卸载，我之前卸载过，但可能没卸载干净，所以一直没法打开docker。
* 错误界面之前忘了截图，没法贴出来。后来我又重新安装了`VirtualBox`，Ok，正常打开Docker了。

`VirtualBox`下载地址：
[https://download.virtualbox.org/virtualbox/5.2.18/VirtualBox-5.2.18-124319-OSX.dmg](https://download.virtualbox.org/virtualbox/5.2.18/VirtualBox-5.2.18-124319-OSX.dmg)

# 使用例子
如果终端运行 `docker version`、`docker info` 都正常的话，开始尝试安装docker镜像并运行
- 运行一个`Nginx`
```
$ docker run -d -p 80:80 --name hyuga-server nginx
输出容器ID：
7da352e6435aeddbd414f8d8062dac3de6e406b76cf5b0d586904f34945e91fb
```
- 这条指令的意思是：以nginx为镜像副本，创建一个名为webserver的容器，内外部访问端口都是80
- docker run：启动容器
- `-d`：使容器在后台运行
- `-p`：
    - 第一个80是本机浏览器要访问的端口，第二个是容器服务的端口
    - 意思是：能通过本机80端口印射到容器的80端口
- `--name hyuga-server`：是以nginx镜像为副本创建一个容器，名为hyuga-server

服务运行后，浏览器访问 http://localhost
如果看到下面这个页面，说明`Nginx`镜像安装并启动成功。
![](/img/2018-08-23/5.png)

指令：
```
# 安装并启动nginx的docker镜像
$ docker run -d -p 80:80 --name webserver nginx
# 停止服务器并删除nginx的docker镜像
$ docker stop webserver
$ docker rm webserver
```
注意：安装并启动nginx后，执行stop指令，如果不删除rm，不能再次执行第一条run指令，会提示
```
Error response from daemon: Conflict. The container name "/webserver" is already in use by container "a25129cb2f00c3233564ac24047c0b3f4fd9b73c04a40cafc914a62aad218f33". You have to remove (or rename) that container to be able to reuse that name.
```
翻译如下：
    错误响应守护进程:冲突。容器的名字“/网络服务器”“a25129cb2f00c3233564ac24047c0b3f4fd9b73c04a40cafc914a62aad218f33”已经在使用的容器。你必须删除(或者重命名)容器能够重用这个名字。

所以需要先执行rm操作

好了，docker的第一篇记录就到这里了！！！
