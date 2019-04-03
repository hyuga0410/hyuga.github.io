---
layout:       post
title:        "Mac ITerm2神器配色方案"
subtitle:     "教你配置ITerm2神器个人最喜欢的配色方案"
date:         2019-01-10 15:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-5.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - alfred
---

# 前言
个人完全是用ITerm2代替了mac自带的终端，支持各种个性化定制，做完配色后，使用起来更舒服。

教程来自简书，非常感谢作者【Bill_Wang】分享 - [ITerm2配色方案](https://www.jianshu.com/p/33deff6b8a63)

下面介绍下配置步骤。

# 配色步骤

#### 打开配置文件
打开ITerm2终端，执行 `vim ~/.bash_profile`

贴入以下代码段！

{% highlight java %}
#enables colorin the terminal bash shell export
export CLICOLOR=1

#setsup thecolor scheme for list export
export LSCOLORS=gxfxcxdxbxegedabagacad

#sets up theprompt color (currently a green similar to linux terminal)
export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;36m\]\w\[\033[00m\]\$     '
#enables colorfor iTerm
export TERM=xterm-256color
{% endhighlight %}

#### 设置vim可配色
ITerm2终端输入 `vim .vimrc`

贴入以下代码段！
 
{% highlight java %}
syntax on
set number
set ruler
{% endhighlight %}

#### 下载ITerm2 Color主题包
[Iterm2-color-schemes网址](https://iterm2colorschemes.com/)

[.zip 包下载](https://github.com/mbadolato/iTerm2-Color-Schemes/zipball/master)

[.tar.gz 包下载](https://github.com/mbadolato/iTerm2-Color-Schemes/tarball/master)

#### 设置ITerm2主题
如下操作：
`iTerm2->Preferences->Profiles->Color` 选择 `Color Presets->import` 到下载并解压好的主题目录下schemes目录下选择你要的主题导入。

schemes目录下包含了所有下载下来的主题文件，选中你要想要的主题导入即可。

文件地址：`/Users/xxx/Downloads/mbadolato-iTerm2-Color-Schemes-7e1c743/schemes/Solarized\ Dark\ Higher\ Contrast.itermcolors`

![](/img/2019/2019-01/iterm-1.png)

导入后，选择你要的主题。

我是按作者推荐的使用了 `Solarized Dark Higher Contrast` 主题，个人感觉真的挺舒服的。

主题效果如下！

![](/img/2019/2019-01/iterm-2.png)

如果设置后无效，建议关掉ITerm2，然后重下ITerm2试试。






