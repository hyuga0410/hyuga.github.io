---
layout:       post
title:        "curl命令"
subtitle:     "curl命令查看request相关信息"
date:         2020-01-22 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-15.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - curl
---

#### 前言
有时为了在服务器上便捷的验证接口响应情况，会直接使用curl命令进行查看，下面介绍一些常用的。

以下内容大多来自于[curl基本用法](https://www.cnblogs.com/liuxia912/p/10960555.html)

#### 命令
###### -I 

> 查看HTTP 响应头信息

`curl "http://www.baidu.com" -I`

响应信息：
```shell script
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
Connection: keep-alive
Content-Length: 277
Content-Type: text/html
Date: Wed, 22 Jan 2020 03:47:18 GMT
Etag: "575e1f6d-115"
Last-Modified: Mon, 13 Jun 2016 02:50:21 GMT
Pragma: no-cache
Server: bfe/1.0.8.18
```

###### -v

`curl "http://www.baidu.com" -v`

```shell script
* Rebuilt URL to: http://www.baidu.com/
*   Trying 14.215.177.38...
* TCP_NODELAY set
* Connected to www.baidu.com (14.215.177.38) port 80 (#0)
> GET / HTTP/1.1
> Host: www.baidu.com
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Accept-Ranges: bytes
< Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
< Connection: keep-alive
< Content-Length: 2381
< Content-Type: text/html
< Date: Wed, 22 Jan 2020 03:47:34 GMT
< Etag: "588604dc-94d"
< Last-Modified: Mon, 23 Jan 2017 13:27:56 GMT
< Pragma: no-cache
< Server: bfe/1.0.8.18
< Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/
<
<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=百度一下 class="bg s_btn"></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a> <a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a> </noscript> <script>document.write('<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>');</script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>关于百度</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>使用百度前必读</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>
```

###### 测试网页返回值
`curl -o /dev/null -s -w %{http_code} www.baidu.com`

显示网页返回状态码，200表示成功。

###### 下载网页内容到文件
`curl http://www.baidu.com >> /Users/xxx/Downloads/linux.html`

###### 指定proxy服务器以及其端口

curl支持通过内置option：-x支持设置代理
       
`curl -x 192.168.0.1:10086 http://www.baidu.com`

###### 保存http的response里面的cookie信息

`curl -c cookie.txt http://www.baidu.com`

执行之后，cookie信息就被保存到 cookie.txt 文件里面了。

###### 保存http的response里面的header信息

`curl -D cookied.txt http://www.baidu.com`

###### 使用cookie去鉴定用户访问权限

`curl -b cookiec.txt http://www.baidu.com`

###### 模拟浏览器访问

`curl -A "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.0)" http://www.baidu.com`

###### 伪造referer（盗链）

很多服务器在收到请求的时候，会去check referer（源网页）。

curl使用内置option：-e可以指定referer

`curl -e "www.baidu.com" http://mail.linux.com`

#### 请求方式

- GET `curl "https://www.baidu.com/xxx.php?proxy=in_hp&sort=&page=5"`
- POST `curl -d 'post_data=param_value' https://www.baidu.com/ip.php`

#### Post json
`curl -H "Content-Type:application/json" -H "Data_Type:msg" -X POST --data '{"cityCode":"SHENZHEN","isUpdatedByBusinessSide":"N","keyword":"东海"}' https://api.xxx.com/dict-api/apiGarden/query`

- --data（即-d）指定的参数必须符合json格式
- -H 指定headers头的时候必须单个使用，即一个-H指定一个头字段信息，如上crul示例那样。

#### 注意

get请求curl需要url前后加上"，否则&开始后面会被截取掉。