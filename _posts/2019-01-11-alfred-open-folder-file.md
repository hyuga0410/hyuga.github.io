---
layout:       post
title:        "Alfred之Workflows快捷搜索文件"
subtitle:     "Alfred神器工作流， 快捷打开文件夹和定位文件"
date:         2019-01-11 15:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-10.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - alfred
---

# 前言
当在mac上拿到一个文件的路径或一个文件夹的路径时，怎么快速的通过`Finder`打开或定位，减少不必要的操作时间。

栗子咯：`/Users/xxx/Downloads/test.txt`

之前没有好的法子，要么通过IDEA工具自带的操作入口打开文件，要么打开ITerm2终端，通过命令执行！

{% highlight java %}
open /Users/xxx/Downloads/test.txt
{% endhighlight %}

通过IDEA工具自带就不说了，还不算特别麻烦，但是第二种方式就真的太繁琐了。。。

`复制路径` --> `打开ITerm2` --> `输入指令执行`

# 准备上天
打开Alfred，打开工作流，自行创建自定义工作流开始！

- 打开Alfred工具，选中工作流界面
![](/img/2019/2019-01/workflows-1.png)
- 左下角新建一个新的工作流`Blank Workflow`
![](/img/2019/2019-01/workflows-2.png)
- 填入信息，这里还可以拖入图片生成图标
![](/img/2019/2019-01/workflows-3.png)
- 在新的工作流定制框中，点击鼠标右键，新建热键`Hotkey`
![](/img/2019/2019-01/workflows-4.png)
- 自定义热键
![](/img/2019/2019-01/workflows-5.png)
- 新建工作流`Actions` -> `Reveal File in Folder`
![](/img/2019/2019-01/workflows-6.png)
- 将两个模块连线，完成
![](/img/2019/2019-01/workflows-7.png)

# 起飞

只需两步，完美！

- 选中`/Users/xxx/Downloads/test.txt`
- 快捷键`⌘ + ⌥ + O`
- 搞定！！！


