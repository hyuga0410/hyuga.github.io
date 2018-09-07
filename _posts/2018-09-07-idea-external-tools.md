---
layout:       post
title:        "IDEA之External Tools分享"
subtitle:     "Intellij IDEA的外部工具配置分享"
date:         2018-09-07 22:42:05
author:       "Hyuga"
header-img:   "img/2018-09/head-top-2.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - docker
---

# 前言
工作中经常需要修改某个类，并将编译后的文件抽取到预发布环境验证，有个一步骤就是要从idea中复制类名然后到项目文件夹中逐层点击定位文件，过程相当繁琐。

所以查了下`IDEA`的外部工具，竟发现支持自定义组装指令，并提供很友好的操作方式。

只需要先自定义配置好指令，然后在当前编辑框或者选中文件，然后点右键，选中自定义的指令执行即可。

很舒服，下面贴出配置步骤和个人配置！！！

# 配置及使用
打开idea偏好设置

![](/img/2018-09/external-tools-1.png)

打开Tools -> External Tools，点击左下角的`+`，自行添加工具指令即可

![](/img/2018-09/external-tools-2.png)

配置完成后apply保存，然后在编辑窗口或者选中文件，点右键，鼠标滑至External Tools处，就会显示你自定义的指令

![](/img/2018-09/external-tools-3.png)

点击，执行，👌

# 个人配置
![](/img/2018-09/external-tools-4.png)

![](/img/2018-09/external-tools-5.png)

![](/img/2018-09/external-tools-6.png)

![](/img/2018-09/external-tools-7.png)

![](/img/2018-09/external-tools-8.png)

`Open Class Folder`这个指令比较特殊，使用的并不是jdk的指令，而是mac系统的open指令，目的在于打开当前选中的java文件编译后class文件所在目录，便于文件定位抽取。

# 注意
上面这几个是针对于操作Java文件配置的，如果你需要操作其他类型的文件而发现指令失败，请自行百度。
以上指令为个人配置，仅提供做参考！！！

还有，配置后的指令是可以在idea中设置快捷键的，如果你用得频繁，可以考虑配下快捷键。

![](/img/2018-09/external-tools-9.png)

更多的jdk指令、系统指令组合，就等各位去发掘吧。