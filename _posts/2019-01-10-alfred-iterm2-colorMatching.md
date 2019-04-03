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
export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;36m\]\w\[\033[00m\]\$ --> '
#enables colorfor iTerm
export TERM=xterm-256color
{% endhighlight %}

执行`source ~/.bash_profile`使配置生效。

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

#### 设置
新安装的iTerm `vim xxx.sh`编辑脚本时，无法通过鼠标滚动

`sudo vi ~/.vimrc` 

在 `~/.vimrc` 中添加这个指令:

`set mouse=a`

#### 继续整合更好的配置
上面配置完成后，其实页面体验已经很好了，但是有些细节还是用起来不舒服。

比如进入git项目路径，不会默认展示当前分支名，这点不是很好。还有就是没法命令行`tab`不全，自动提示符。

所以，再继续整合`iTerm2 + oh-my-zsh` + `solarized`配色方案。

- 安装oh-my-zsh
 
 `github连接：https://github.com/robbyrussell/oh-my-zsh`
 
 使用 crul 安装：
```jshelllanguage
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
``` 

安装成功后，`vim ~/.zshrc`，修改主题为 `agnoster`.

```jshelllanguage
ZSH_THEME="agnoster"
```

- 安装主题
因为上面安装完`oh-my-zsh`后，终端会出现乱码，这个时候就需要下载一个特殊的/优秀的主题包了。

    - 下载 [Meslo](https://github.com/powerline/fonts/blob/master/Meslo%20Slashed/Meslo%20LG%20M%20Regular%20for%20Powerline.ttf)字体。
    - 安装字体到系统字体库。
    - 应用字体到iTerm2。（iTerm -> Preferences -> Profiles -> Text -> Change Font）选择`Meslo`，字体大小按个人喜好。
    - 重新打开iTerm窗口，搞定。
    
[https://ethanschoonover.com/solarized/](https://ethanschoonover.com/solarized/)这上面有很多配色方案，按个人喜欢去下载。    
    
- 设置iTerm2为默认终端
（菜单栏）iTerm2 -> Make iTerm2 Default Term
![](https://images2015.cnblogs.com/blog/1110743/201706/1110743-20170617160006728-2115137217.png)

- 设置全局热键
打开偏好设置preference，选中Keys，勾选Hotkey下的Show/hide iTerm2 with a system-wide hotkey，将热键设置为`option+.` 

这样你就可以通过`option+.` 全局热键来打开或关闭iTerm2窗口，非常方便。

![](https://images2015.cnblogs.com/blog/1110743/201706/1110743-20170617161612993-674443833.png)




## 注意

`oh-my-zsh`教程和图片来源于[Mac下终端配置（item2 + oh-my-zsh + solarized配色方案）](https://www.cnblogs.com/weixuqin/p/7029177.html)




