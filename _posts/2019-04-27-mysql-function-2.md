---
layout:       post
title:        "Mysql获取字符串中的数字函数"
subtitle:     "Mysql非常用函数记录"
date:         2019-04-27 15:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-20.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - mysql
---

# 前言
这是一个Mysql获取字符串中的数字函数方法。

文章内容来自博客园！ [【Mysql获取字符串中的数字函数方法和调用】](https://www.cnblogs.com/wjm956/p/6605842.html)

# 函数
{% highlight sql %}
CREATE FUNCTION GetNum (Varstring varchar(50))
RETURNS varchar(30)
BEGIN
DECLARE v_length INT DEFAULT 0;
DECLARE v_Tmp varchar(50) default '';
set v_length=CHAR_LENGTH(Varstring);
WHILE v_length > 0 DO
IF (ASCII(mid(Varstring,v_length,1))>47 and ASCII(mid(Varstring,v_length,1))<58 ) THEN
set v_Tmp=concat(v_Tmp,mid(Varstring,v_length,1));
END IF;
SET v_length = v_length - 1;
END WHILE;
RETURN REVERSE(v_Tmp);
END;
{% endhighlight %}

# 函数调用
{% highlight sql %}
select GetNum(字段) from table;-- GetNum中的字段名不需要用引号括起来
{% endhighlight %}