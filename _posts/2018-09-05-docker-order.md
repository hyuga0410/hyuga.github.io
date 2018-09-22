---
layout:       post
title:        "Docker一些概念和指令"
subtitle:     "常用docker指令整理"
date:         2018-09-05 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-14.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - docker
---

# 前言
> Docker可以简单理解为一个虚拟机管理器，就像VMware相似，用于管理各个虚拟系统（容器）.

安装docker后，本机会有一个`镜像管理仓库`和`容器管理仓库`
- `docker images` 可以查看当前本地镜像仓库已安装的镜像
- `docker ps` 可以查看当前本地容器仓库正在运行的容器
- `docker ps -a` 可以查看当前本地容器仓库已安装的容器

**一个容器的生命周期：**
- 从远程仓库拉取镜像到本地镜像仓库
- 根据镜像创建容器放到本地容器仓库
- 启动容器
- 关闭容器
- 删除容器
- 删除镜像

# 常规指令
- 查看当前系统docker信息`docker info`
- 拉取docker镜像`docker pull image_name`
- 查看宿主机(本机)的镜像`docker images`
- 查看当前正在运行的容器`docker ps`
- 查看所有容器`docker ps -a`
- 启动、停止、重启容器命令
    - `docker start container_name/container_id`
    - `docker stop container_name/container_id`
    - `docker restart container_name/container_id`
- 进入正在运行的容器`docker attach container_name/container_id`
- 删除容器`docker rm container_name/container_id`
- 删除镜像`docker rmi docker.io/tomcat:7.0.77-jre7 或者  docker rmi b39c68b7af30`

---
- 删除所有容器
```
docker rm `docker ps -a -q`
sudo docker rm $(sudo docker ps -a -q)
```

- 删除所有镜像
```
docker rmi `docker images -q`
```

# docker安装tomcat
- 搜索tomcat镜像 `docker search tomcat`
- 安装tomcat镜像 `docker pull tomcat`
- 查看已安装的镜像 `docker images`
- 启动tomcat容器 `docker run -p 8080:8080 tomcat:latest`
    - p标识端口号
    - 第一个8080是浏览器访问的端口，如果冲突会提示`docker: Error response from daemon: Bad response from Docker engine.`，改下端口即可
    - 第二个8080是tomcat启动的服务端口
    - tomcat:last last是指定的tomcat的标签，相同的镜像可以指定不同的标签以做区分。

启动完成后，可在浏览器中访问 `localhost:8080`，可以看到tomcat的欢迎界面.

![](/img/2018/2018-09/welcome-tomcat.png)
- 查看正在运行的tomcat容器 `docker ps`

![](/img/2018/2018-09/docker-ps.png)

# 暂停
个人精力有限，docker入门不难，但稍微深入花费精力太多，暂且不折腾了，等以后有精力了再整理。