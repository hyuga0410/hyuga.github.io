---
layout:       post
title:        "MacOs升级后Read-Only filesystem"
subtitle:     "无法操作根目录文件解决方案"
date:         2020-02-03 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-22.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - mac
---

## 前言
`Mac OS` 升级 `macOS Catalina` 后，发现根目录的log文件夹不见了，而且无法再创建，提示`Read-Only filesystem`，用sudo创建也不行。

经过查资料发现是新版系统默认对根目录文件做了只查权限控制，对于用户来说是更安全了，但对于开发者来说，则是太刻板了。

![](/img/2020/2020-01/mac-1.png)

## 解开封印
- 关闭SIP
- 然后输入`sudo mount -uw /`
- 创建文件夹添加权限
- 最后启用SIP

一、怎么关闭SIP？

关闭电脑，按住电源键启动并按住 Command + R 进入 macOS 恢复系统，等检索完磁盘，在左上角的实用工具里面点击终端，加上下面这一句代码。

```
csrutil disable
```

按下回车键，看见successfully...这时已经关闭SIP。

二、重新启动电脑
重启电脑后，打开终端，执行：
```
sudo mount -uw
```

三、创建目录
```
sudo mkdir /log
sudo chmod 777 log
```

四、重新开启SIP
按第一步进入恢复系统，执行：
```
csrutil enabled
```

PS：作为开发人员就没必要再开启SIP了。

