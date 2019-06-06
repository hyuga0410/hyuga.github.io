---
layout:       post
title:        "最新版IDEA IntelliJ破解"
subtitle:     "最新版IDEA IntelliJ破解"
date:         2019-06-06 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-12.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - java
---

感谢大神 [lanyus](http://idea.lanyus.com/)

---
> 以下操作为Mac OS系统

#### IDEA版本
![](/img/2019/2019-06/idea-pojie-1.png)

#### 破解包
[下载地址](https://pan.baidu.com/s/1dY9ebfQ8BsxwV3v8_Xuuug)

#### 破解步骤

1. 打开`访达`（finder）
2. 打开左侧菜单栏中的`应用程序`
3. 找到`IntelliJ IDEA`应用图标，右键选择`显示包内容`
4. 依次打开`Contents` -> `bin`
5. 将上面的破解包JetbrainsIdesCrack-4.2-release-sha1.jar拖入到bin目录下
6. 打开`idea.vmoptions`文件，贴入以下配置~~~~
7. 打开`IntelliJ IDEA`应用，提示需要输入注册码
8. 打开
  [http://idea.lanyus.com](http://idea.lanyus.com)，点击`获取注册码`，拷贝注册码，黏贴到IDEA输入框，提交应用，搞定！

> 第三步

![](/img/2019/2019-06/idea-pojie-3.png)

> 第四步

![](/img/2019/2019-06/idea-pojie-4.png)

> 第七步配置信息

{% highlight java %}
-Xms1024m
-Xmx2048m
-XX:ReservedCodeCacheSize=240m
-XX:+UseCompressedOops
-Dfile.encoding=UTF-8
-XX:+UseConcMarkSweepGC
-XX:SoftRefLRUPolicyMSPerMB=50
-ea
-Dsun.io.useCanonCaches=false
-Djava.net.preferIPv4Stack=true
-Djdk.http.auth.tunneling.disabledSchemes=""
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-Xverify:none

-XX:ErrorFile=$USER_HOME/java_error_in_idea_%p.log
-XX:HeapDumpPath=$USER_HOME/java_error_in_idea.hprof

-javaagent:JetbrainsIdesCrack-4.2-release-sha1.jar
{% endhighlight %}

> 第八步获取注册码

![](/img/2019/2019-06/idea-pojie-2.png)

#### 完成

![](/img/2019/2019-06/idea-pojie-5.png)

#### 参考

[ IntelliJ IDEA热部署插件JRebel免费激活图文教程](https://www.jiweichengzhu.com/article/a45902a1d7284c6291fe32a4a199e65c)

http://idea.lanyus.com