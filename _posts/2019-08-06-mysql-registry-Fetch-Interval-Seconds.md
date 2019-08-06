---
layout:       post
title:        "Mysql疑难杂症"
subtitle:     "开发过程遇到的一些mysql相关的问题"
date:         2019-07-25 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-22.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - mysql
---

#### mysql5.7报错this is incompatible with sql_mode=only_full_group_by

昨天发现`rc`的`mysql`和线上版本不一致，重装5.7的`mysql`后，发现启动会报某条`sql`错误，经排查，发现是最新版的`mysql5.7.x`版本，默认是开启了 `only_full_group_by` 模式。

一旦开启这个模式，原先的 group by语句就报错。也就是上面提示的错误。

怎么关闭这个特性呢？

`vim /etc/my.cnf`

vim下光标移到最后，添加如下：

{% highlight java %}
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
{% endhighlight %}
 
重启mysql服务，顺利解决。 

`service mysqld restart` 或 `/etc/inint.d/mysqld start`