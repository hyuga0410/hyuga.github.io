---
layout:       post
title:        "Docker安装过程记录"
subtitle:     "学习docker安装使用过程记录"
date:         2018-08-23 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-9.jpg"
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

![](/img/2018/2018-08/23-1.png)
拖拽安装，搞定！！！
![](/img/2018/2018-08/23-2.png)
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
![](/img/2018/2018-08/23-3.png)
![](/img/2018/2018-08/23-4.png)
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
![](/img/2018/2018-08/23-5.png)

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

# 安装mariadb
Docker 中提供了很多 MariaDB 的镜像，可以通过以下命令查询

`docker search mariadb`

我们使用官方提供的镜像，以下为获取下载镜像，默认获取最新的版本
`docker pull mariadb`

接下来，创建一个 MariaDB 容器

`docker run --name mariadb_hyuga -e MYSQL_ROOT_PASSWORD=xxx -d mariadb`

通过官网mariadb镜像创建一个本地mariadb容器，密码为xxx

> 13261d65d273ea36638d230ea8af9f8d6d66a6d41a154d0db598563769699977

启动容器：`docker start mariadb_hyuga`

执行 `docker ps -a`，可以看到当前本地容器仓库已安装的容器
> CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
  13261d65d273        mariadb             "docker-entrypoint.s…"   37 seconds ago      Up 36 seconds              3306/tcp            mariadb_hyuga

上面刚安装的 MariaDB 【mariadb_hyuga】 容器已经在运行中。

接下来进入【mariadb_hyuga】容器中。

`docker exec -it 13261d65d273 bash` 或者 `docker exec -it mariadb_hyuga bash`都可以进入容器。

显示`root@13261d65d273:/#`表示已经进入容器。

接着进入数据库：`mysql -u root -p`，输入密码`xxx`

这时的 MariaDB 已经可以正常使用，但是无法远程连接，因此我们需要映射端口来让我们的数据库能被远程访问。

`docker run -d -P --name mariadb_connect -e MYSQL_ROOT_PASSWORD=xxx mariadb`
> 2a84e14a1fba2ae96a25403de269946c4baf00af5cf1453c0707fb7a74a30d55

通过 -P 参数，Docker 会为我们自动分配一个未被使用的端口，这里是 32768.

执行`docker ps -a`可以看到，多了一个容器mariadb_connect，用于将容器`mariadb_hyuga`端口映射出去。

> CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
  2a84e14a1fba        mariadb             "docker-entrypoint.s…"   52 seconds ago      Up 50 seconds       0.0.0.0:32768->3306/tcp   mariadb_connect
  74ee06107710        mariadb             "docker-entrypoint.s…"   20 minutes ago      Up 20 minutes       3306/tcp                  mariadb_hyuga

接下来，尝试使用数据库客户端工具来测试一下是否能连接。

如果需要指定映射端口，可以使用这个命令`-p hostPort:containerPort`

如：`docker run -d -p 3306:3306 --name mariadb_connect -e MYSQL_ROOT_PASSWORD=xxx mariadb`

第一个3306是主机的端口，第二个3306是容器的端口

接着用数据库连接工具连接就可以了：127.0.0.1:3306

#### 查看docker某个容器的ip
`docker inspect mariadb_hyuga | grep IPAddress`

输出如下，表明mariadb_hyuga的ip地址是`172.17.0.2`
{% highlight java %}
"SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
{% endhighlight %}

# docker安装redis
拉取最新的redis镜像`docker pull redis`

查看本地已拉取的镜像`docker images`，可以看到两个镜像，分别是mariadb和reids

启动redis：`docker run -p 6379:6379 -d redis:latest redis-server`，并映射好端口，其实上面的mariadb_hyuga也可以用这中方式启动映射。

{% highlight java %}
菜鸟教程：
docker run -p 6379:6379 -v $PWD/data:/data  -d redis:3.2 redis-server --appendonly yes

命令说明：
-p 6379:6379 : 将容器的6379端口映射到主机的6379端口
-v $PWD/data:/data : 将主机中当前目录下的data挂载到容器的/data
redis-server --appendonly yes : 在容器执行redis-server启动命令，并打开redis持久化配置
{% endhighlight %}

{% highlight java %}
docker exec -ti 容器名 redis-cli

docker exec -ti 容器名 redis-cli -h localhost -p 6379
docker exec -ti 容器名 redis-cli -h 127.0.0.1 -p 6379
docker exec -ti 容器名 redis-cli -h 172.17.0.3 -p 6379

// 容器名可以通过 docker ps -a 查看，最后一列就是。
// 注意，这个是容器运行的ip，可通过 docker inspect redis_s | grep IPAddress 查看
{% endhighlight %}


使用redis镜像执行redis-cli命令连接到刚启动的容器,主机IP为172.17.0.1

`docker exec -it redis_s redis-cli`
127.0.0.1:6379>
如果连接远程：

`docker exec -it redis_s redis-cli -h 192.168.1.100 -p 6379 -a your_password` //如果有密码 使用 -a参数
192.168.1.100:6379>

