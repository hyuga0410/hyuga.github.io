---
layout:       post
title:        "IDEA使用设置"
subtitle:     "慢慢记录，个人喜好而已！"
date:         2019-04-18 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-4.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - idea
---

## IDEA实用设置

> 以下内容大多摘录自博客园博主（请叫我大表哥）之【[IntelliJ IDEA 常用设置 (二)
](https://www.cnblogs.com/wangmingshun/p/6427088.html)】

这一篇博文真的是很用心的在做，图文并茂！

以下仅供自我记录，推荐直接阅读原博主系列文章。

#### 自动注释的缩进
- 打开`Preferences...`  `command + ,`
- Editor -> Code Style -> Java
- 选择 Code Generation
- 取消 Line comment at first column 和 Block comment at first column 的选中即可

![](/img/2019/2019-04/idea-1.png)

#### 自动导包
![](/img/2019/2019-04/idea-2.png)

- 勾选标注 1 选项，IntelliJ IDEA 将在我们书写代码的时候自动帮我们优化导入的包，比如自动去掉一些没有用到的包。 

- 勾选标注 2 选项，IntelliJ IDEA 将在我们书写代码的时候自动帮我们导入需要用到的包。但是对于那些同名的包，还是需要手动 Alt + Enter 进行导入的

#### 代码提示去除大小写区分
![](/img/2019/2019-04/idea-3.png)

#### 设置IntelliJ IDEA显示内存
![](/img/2019/2019-04/idea-4.png)

#### 显示行数和方法线
![](/img/2019/2019-04/idea-5.png)

#### 代码行宽度超出限制时设置自动换行
- 在输入代码时触发，随着输入的字符的增加，当代码宽度到达界线时，IDEA会自动将代码换行。
![](/img/2019/2019-04/idea-6.png)

- 在格式化Java代码时触发，确保代码没有超过宽度界线。
![](/img/2019/2019-04/idea-7.png)

#### IDEA默认Settings设置
![](/img/2019/2019-04/idea-8.png)