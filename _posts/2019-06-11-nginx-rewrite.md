---
layout:       post
title:        "Nginx rewrite重定向"
subtitle:     "Nginx rewrite重定向应用场景"
date:         2019-06-11 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-29.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - nginx
---

##  域名重定向

1. 阿里云域名解析设置`www.hyuga.com`，指向负载均衡服务ip
2. 在对应的云服务上nginx配置如下
3. 浏览器访问`www.hyuga.com`将自动跳转到`http://shenzhen.hyuga.com`

![](/img/2019/2019-06/nginx-rewrite-1.png)

{% highlight java %}
server{
       listen  80;
       server_name www.hyuga.com;

       rewrite ^/(.*)$ http://shenzhen.hyuga.com/$1 permanent;
}
{% endhighlight %}

## 域名反向代理本地服务

1. springboot服务jar包部署，端口8086
2. 期望访问域名`test.shenzhen.hyuga.com`能访问到本地jar服务
3. 期望访问域名`test.shenzhen.hyuga.com/index`能重定向到`http://test.shenzhen.hyuga.com`
   
配置如下：

{% highlight java %} 
server{ 
    listen 80; 
    server_name test.shenzhen.hyuga.com; 
    access_log logs/business-access.log;

    rewrite ^/index$ http://test.shenzhen.hyuga.com permanent;

    location ~ / {
            # 增加代理指向本地端口
            proxy_pass http://localhost:8086;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
    }
}
{% endhighlight %}

# 资料参考

[【技术颜良】常用nginx rewrite重定向-跳转实例](常用nginx
rewrite重定向-跳转实例)

以下内容为【技术颜良】常用nginx rewrite重定向-跳转实例 内容，谢谢分享：

- 将`www.myweb.com/connect` 跳转到`connect.myweb.com`

{% highlight java %}
rewrite ^/connect$ http://connect.myweb.com permanent;

rewrite ^/connect/(.*)$ http://connect.myweb.com/$1 permanent;
{% endhighlight %}

- 将`connect.myweb.com` 301跳转到`www.myweb.com/connect/` 

{% highlight java %}
if ($host = "connect.myweb.com"){
    rewrite ^/(.*)$ http://www.myweb.com/connect/$1 permanent;
}
{% endhighlight %}


- `myweb.com` 跳转到`www.myweb.com`

{% highlight java %}
if ($host != 'www.myweb.com' ) { 
    rewrite ^/(.*)$ http://www.myweb.com/$1 permanent; 
}
{% endhighlight %}

- `www.myweb.com/category/123.html` 跳转为 `category/?cd=123`

{% highlight java %}
rewrite "/category/(.*).html$" /category/?cd=$1 last;
{% endhighlight %}
 
-  `www.myweb.com/admin/` 下跳转为`www.myweb.com/admin/index.php?s=`

{% highlight java %}
if (!-e $request_filename){
    rewrite ^/admin/(.*)$ /admin/index.php?s=/$1 last;
}
{% endhighlight %}

- 在后面添加/index.php?s=

{% highlight java %}
if (!-e $request_filename){
    rewrite ^/(.*)$ /index.php?s=/$1 last;
}
{% endhighlight %}

-  `www.myweb.com/xinwen/123.html` 等xinwen下面数字+html的链接跳转为404

{% highlight java %}
rewrite ^/xinwen/([0-9]+)\.html$ /404.html last;
{% endhighlight %}

-  `http://www.myweb.com/news/radaier.html` 301跳转
`http://www.myweb.com/strategy/`

{% highlight java %}
rewrite ^/news/radaier.html http://www.myweb.com/strategy/ permanent;
{% endhighlight %}

- 重定向 链接为404页面

{% highlight java %}
rewrite http://www.myweb.com/123/456.php /404.html last;
{% endhighlight %}

- 禁止htaccess

{% highlight java %}
location ~//.ht {
     deny all;
}
{% endhighlight %}

- 可以禁止/data/下多级目录下.log.txt等请求;

{% highlight java %}
location ~ ^/data {
     deny all;
}
{% endhighlight %}

- 禁止单个文件

{% highlight java %}
location ~ /www/log/123.log {
    deny all;
}
{% endhighlight %}

-  `http://www.myweb.com/news/activies/2014-08-26/123.html` 跳转为
`http://www.myweb.com/news/activies/123.html`

{% highlight java %}
rewrite ^/news/activies/2014\-([0-9]+)\-([0-9]+)/(.*)$ http://www.myweb.com/news/activies/$3 permanent;
{% endhighlight %}

- nginx多条件重定向rewrite

如果需要打开带有play的链接就跳转到play，不过/admin/play这个不能跳转

{% highlight java %}
if ($request_filename ~ (.*)/play){ set $payvar '1';}
if ($request_filename ~ (.*)/admin){ set $payvar '0';}
if ($payvar ~ '1'){
      rewrite ^/ http://play.myweb.com/ break;
}
{% endhighlight %}
    
-  `http://www.myweb.com/?gid=6` 跳转为`http://www.myweb.com/123.html`

{% highlight java %}
if ($request_uri ~ "/\?gid\=6"){
    return  http://www.myweb.com/123.html;
}
{% endhighlight %}
 
正则表达式匹配，其中：

* ~ 为区分大小写匹配
* ~* 为不区分大小写匹配
* !~和!~*分别为区分大小写不匹配及不区分大小写不匹配

文件及目录匹配，其中：

* -f和!-f用来判断是否存在文件
* -d和!-d用来判断是否存在目录
* -e和!-e用来判断是否存在文件或目录
* -x和!-x用来判断文件是否可执行

flag标记有：

* last 相当于Apache里的[L]标记，表示完成rewrite
* break 终止匹配, 不再匹配后面的规则
* redirect 返回302临时重定向 地址栏会显示跳转后的地址
* permanent 返回301永久重定向 地址栏会显示跳转后的地址
