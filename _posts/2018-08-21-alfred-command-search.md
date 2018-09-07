---
layout:       post
title:        "command-search-alfred神器"
subtitle:     "一款alfred插件工具，指令管理神器"
date:         2018-08-21 15:42:05
author:       "Hyuga"
header-img:   "img/2018/2018-08/head-top-4.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - alfred
---

**转载自：** [https://github.com/mrdear/command-search-alfred](https://github.com/mrdear/command-search-alfred)

> **感谢作者[屈定](https://mrdear.cn/)的分享**

---

# Alfred一款命令搜索workflow

### 介绍
日常开发中要记住的一些长命令太多,打起来很费事,因此使用workflow帮助管理命令.

该workflow把命令分为key -> values形式,如下所示,key属于大分类,匹配到key后会显示其下全部value.
```json
  {
    "key":"git-common",
    "values":[
      {
        "cmd":"git branch -r | sed 's/origin\///g' | grep '/' | xargs git push origin --delete",
        "remark":"git批量删除远程分支"
      }
    ],
    "remark":"git通用命令"
  }
```


主要功能:
1. cmd(触发关键词)->搜索key->选择value->复制到粘贴板(可以自动粘贴)
2. cmd(触发关键词)->open->选择打开命令配置->调用你喜欢的编辑器打开命令配置的json(主要是alfred添加数据不太好用)


### 数据保存
该插件对应json命令数据都是外置的(便于云端保存,丢到同步盘中即可),因此自己指定一个路径后,以参数形式传入即可.

也由于json外置,如果你觉得workflow添加太麻烦的话,直接改这个json,添加对应的数据即可.

![](/img/2018/2018-08/1.png)


### 演示

![](/img/2018/2018-08/yulan.gif)


### 其他问题

**1.不想要自动粘贴**

在alfred插件中选择粘贴这个环节

![](/img/2018/2018-08/3.png)

然后勾掉自动粘贴即可.

![](/img/2018/2018-08/4.png)

### 更新记录

2018.02.26

关键词匹配去除`-_空白`特殊字符

---

以上为作者[屈定](https://mrdear.cn/)的文章内容，下面为个人使用感受和使用过程中需要注意的地方

## 使用感受
* 惊艳
* 舒服

作者[屈定](https://mrdear.cn/)的这款alfred插件真心好用，再也不用去记住一堆杂乱无章的指令。
该插件还支持指令配置文件路径配置，配合同步云盘多终端也不需要拷贝来拷贝去。

使用步骤作者文章写得很清楚了，这里说点其他需要注意的点：
1. 看到这里的应该都是已经对[alfred](https://www.alfredapp.com/)软件有所了解，使用`workflow`需要先开通`Powerpack`功能，是的，需要付费。
2. data.json的两种配置方式
    * 多终端：使用同步云端工具，自行配置文件路径，实现多终端统一配置
    * 本地：⌥ + space 打开alfred，输入`cmd  `，注意打多一个空格，点击`打开命令配置`，这时会弹出一个文本编辑框，也就是data.js，直接配置就好了
3. 本来还想试着用网址路径配置的，结果发现想多了，最终采取多终端配置的方式。后续将慢慢完善自己的data.json文件！！！

![](/img/2018/2018-08/5.png)