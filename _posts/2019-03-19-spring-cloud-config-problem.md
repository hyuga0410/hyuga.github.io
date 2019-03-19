---
layout:       post
title:        "SpringCloud config ssh方式配置"
subtitle:     "SpringCloud config配置采坑记"
date:         2019-03-19 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-17.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - aliyun
---

# 前言
项目原采用公司内网git仓库管理，后更改使用阿里云code托管。

需要变更配置方式，使用jenkins git clone阿里云code，一开始各种碰壁，最终解决clone问题。

详见上一篇文章：【阿里云Code git clone ssh方式】

接下来就是要更改Spring Cloud config的配置，接下来开始领略跳坑的风采。

# 坑

## 内网调用 
cloud-config-server项目下的bootstrap.yml

{% highlight java %}
spring:
  application:
    name: config-server
  cloud:
    config:
      label: master
      server:
        git:
          uri: http://xxx-public@git.xxx.com/xxx/xxx-micro-services.git
          #http或https方式，可通过启动脚本参数赋值
          search-paths: /config-repo
          username: xxx-public
          password: xxx-public
{% endhighlight %}


脚本参数赋值方式：

{% highlight java %}
nohup $JRE_HOME/bin/java -Xms512m -Xmx1024m -jar -Dspring.cloud.config.server.git.uri=http://xxx-public@git.qfang.com/xxx/xxx-micro-services.git -Dspring.cloud.config.server.git.search-paths=/config-repo -Dspring.cloud.config.server.git.username=xxx-public -Dspring.cloud.config.server.git.password=xxx-public  $JAR_NAME >/dev/null 2>&1 &
{% endhighlight %}

这种方式内网调用，测试环境是没有任何问题的。

但是在阿里云上需要注册一个账号用于拉取源码，还得找个手机号来注册个空账号，太繁琐，放弃。

## ssh调用
后来想着git clone都可以下载ssh形式的源码了，那就用ssh的形式来配置SpringCloud config吧，结果跳坑了。

SpringCloud config配置ssh方式，需要公钥、私钥，反正折腾了很久，最终也没搞清楚到底是哪里出的问题，配置启动正常，无法访问正常文件。

公钥、密钥等等都按官网文档进行配置，也在aliyun上进行了ssh密钥签证，还是搞不定。

具体配置方式如下：

{% highlight java %}
spring:
  application:
    name: config-server
  cloud:
    config:
      label: master
      server:
        git:
          uri: git@code.aliyun.com:xxx-business/config-repo.git
          search-paths: /
          ignoreLocalSshSettings: true
          hostKey: AAAAB3NzaC1yc2EAAAADAQABAAABAQDIMChrvLA7DVM2nVmtigENx... # cat ~/.ssh/id_rsa_aliyun.pub阿里云公钥
          
          hostKeyAlgorithm: ssh-rsa
          # |线是必须的，-----BEGIN RSA PRIVATE KEY-----和-----END RSA PRIVATE KEY-----也是必须的
          privateKey: |
                        -----BEGIN RSA PRIVATE KEY-----
                        Proc-Type: 4,ENCRYPTED
                        DEK-Info: AES-128-CBC,CCA7513A8E8C66B451D414AD9E679C4A

                        WfMhdCm5mdq35R67rb+Oqr3RhIxgCl6pnNjx5GBio6+CWZIcy4p+48SyGdR0ILCa  # cat ~/.ssh/id_rsa_aliyun阿里云密钥
                        ...
                        -----END RSA PRIVATE KEY-----
{% endhighlight %}

配置后，启动无误，访问不到具体文件，配置方面都排查过很多次，可能眼瞎，确实没找到问题。

最终放弃这种方案。

[Git SSH configuration using properties 官网配置](https://cloud.spring.io/spring-cloud-static/Edgware.SR5/single/spring-cloud.html#_git_ssh_configuration_using_properties)

## 物理空间访问
没办法，最后采取把配置文件pull到测试环境/data/www/config-repo文件夹下，SpringCloud-config读取该文件夹下的配置文件。

jenkins脚本如下：
{% highlight java %}
cd /root/.jenkins/jobs/config-repo/
rm -rf xxx-config-repo/
git clone ssh://git@code.aliyun.com:22/xxx-business/xxx-config-repo.git
rm -rf /data/www/config-repo
cp -r xxx-config-repo/config-repo /data/www/
{% endhighlight %}

SpringCloud-config.jar启动脚本如下：
{% highlight java %}
#本地物理环境方式
nohup $JRE_HOME/bin/java -Xms512m -Xmx1024m -jar -Dspring.profiles.active=native -Dspring.cloud.config.server.native.searchLocations=file:/data/www/config-repo  $JAR_NAME >/dev/null 2>&1 &
{% endhighlight %}

# ssh
稍微解释`/Users/{user}/.ssh`路径下的几个文件！

{% highlight java %}
-rw-r--r--  1 user  staff   170B  7 13  2017 config
-rw-------  1 user  staff   3.2K  7 13  2017 id_rsa
-rw-r--r--  1 user  staff   747B  7 13  2017 id_rsa.pub
-rw-------  1 user  staff   1.7K  3 19 15:21 id_rsa_aliyun
-rw-r--r--  1 user  staff   399B  3 19 15:21 id_rsa_aliyun.pub
-rw-r--r--  1 user  staff   5.1K  3 19 18:41 known_hosts
{% endhighlight %}


`cat config` 配置了ssh的信息，具体还没怎么去研究，挖个坑，后面跳。

Host huangzeyuan@qfang.com-GitHub
	HostName github.com
	User huangzeyuan@qfang.com
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/huangzeyuan@qfang.comm-GitHub

`cat id_rsa` 配置了默认的ssh私钥

`cat id_rsa.pub` 配置了默认的ssh公钥

`cat id_rsa_aliyun` 配置了阿里云的ssh私钥

`cat id_rsa_aliyun.pub` 配置了阿里云的ssh公钥

`cat known_hosts` 记录了所有本机连过的远程ssh记录，有时ssh远程有问题，可能是公钥私钥信息变更，需要删除该文件对应连接记录。

以下介绍来自阿里云SSH使用介绍：
- SSH keys

SSH key 可以让你在你的电脑和Code服务器之间建立安全的加密连接。 先执行以下语句来判断是否已经存在本地公钥：

{% highlight java %}
cat ~/.ssh/id_rsa.pub
{% endhighlight %}

如果你看到一长串以 ssh-rsa或 ssh-dsa开头的字符串, 你可以跳过 ssh-keygen的步骤。

提示: 最好的情况是一个密码对应一个ssh key，但是那不是必须的。你完全可以跳过创建密码这个步骤。请记住设置的密码并不能被修改或获取。

你可以按如下命令来生成ssh key：

{% highlight java %}
ssh-keygen -t rsa -C "user0410@163.com"
{% endhighlight %}

这个指令会要求你提供一个位置和文件名去存放键值对和密码，你可以不输入新的位置和文件名，直接点击Enter键会覆盖原id_rsa.pub和id_rsa文件。

也可以输入新的位置和文件名用于存放新生成的公钥密钥对。就比如我把新生成的公约密钥放在~/.ssh/id_rsa_aliyun下，会自动生成id_rsa_aliyun.pub和id_rsa_aliyun

{% highlight java %}
cat ~/.ssh/id_rsa.pub
{% endhighlight %}

复制这个公钥放到你的[个人设置](https://code.aliyun.com/profile/keys)中的SSH/My SSH Keys下，增加SSH私钥。请完整拷贝从ssh-开始直到你的用户名和主机名为止的内容。

这一步是用于绑定本机ssh和阿里云的通信。

至此，就可以在本机或测试环境拉取阿里云CODE源码了。

git clone ssh://git@code.aliyun.com:22/xxx-config-rep.git

![](/img/2019/2019-03/springcloud-config-1.png)