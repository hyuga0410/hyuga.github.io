---
layout:       post
title:        "Mysql获取首字母大写函数、随机数生成"
subtitle:     "Mysql非常用函数记录"
date:         2019-01-26 15:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-16.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - mysql
---

# 前言
最近有个场景，需要在mysql表中生成一个编号，规则是城市区域拼音首字母大写+日期+3位随机数。

试过多种方案后，推荐下面的方法，亲测可用。

获取首字母函数教程来源：[MySQL中文取首字母实现](https://blog.csdn.net/zane3/article/details/77504673)
生成随机数函数教程来源：[mysql生成指定位数的随机数](https://blog.csdn.net/zhou520yue520/article/details/82882994)

# 栗子

- 第一步：建立转化第一个字母的数据库函数firstletter

{% highlight java %}
USE `数据库名`;
DROP function IF EXISTS `firstletter`;

DELIMITER $$
USE `数据库名`$$
CREATE DEFINER=`root`@`%` FUNCTION `firstletter`(name varchar(255)) RETURNS varchar(255) CHARSET utf8
BEGIN
declare result varchar(255);
  set result=elt(interval(conv(hex(left(convert(name using gbk),1)),16,10),
  0xB0A1,0xB0C5,0xB2C1,0xB4EE,0xB6EA,0xB7A2,0xB8C1,0xB9FE,0xBBF7,
        0xBFA6,0xC0AC,0xC2E8,0xC4C3,0xC5B6,0xC5BE,0xC6DA,0xC8BB,
        0xC8F6,0xCBFA,0xCDDA,0xCEF4,0xD1B9,0xD4D1),
         'A','B','C','D','E','F','G','H','J','K','L','M','N','O','P','Q','R','S','T','W','X','Y','Z');
RETURN result;
END$$

DELIMITER ;
{% endhighlight %}

- 第二步：建立将所有中文首字母连接的函数firstconcat

{% highlight java %}
USE `数据库名`;
DROP function IF EXISTS `firstconcat`;

DELIMITER $$
USE `数据库名`$$
CREATE DEFINER=`root`@`%` FUNCTION `firstconcat`(P_NAME VARCHAR(255)) RETURNS varchar(255) CHARSET utf8
BEGIN
DECLARE V_COMPARE VARCHAR(255);
    DECLARE V_RETURN VARCHAR(255);
    DECLARE I INT;
    SET I = 1;
    SET V_RETURN = '';
    while I < LENGTH(P_NAME) do
        SET V_COMPARE = SUBSTR(P_NAME, I, 1);
        IF (V_COMPARE != '') THEN
            #SET V_RETURN = CONCAT(V_RETURN, ',', V_COMPARE);
            SET V_RETURN = CONCAT(V_RETURN, firstletter(V_COMPARE));
            #SET V_RETURN = fristPinyin(V_COMPARE);
        END IF;
        SET I = I + 1;
    end while;
    IF (ISNULL(V_RETURN) or V_RETURN = '') THEN
        SET V_RETURN = P_NAME;
    END IF;
    RETURN V_RETURN;
END$$
DELIMITER ;
{% endhighlight %}

- 函数执行
将上面两个函数的数据库名改正后执行生成函数！

{% highlight java %}
select
  concat(firstconcat(t.froom_no), date_format(t.frent_time, '%m%d%H%m%s'), (SELECT CEILING(RAND()*900+100))) 'no',
  t.fid 'id'
from t_room_back t
{% endhighlight %}

# 生成随机数

> 函数
- RAND()     随机生成 0~1 之间的小数
- CEILING()  向上取整
- FLOOR()    向下取整


**随机数生成**
- 生成 3 位的随机数 `SELECT CEILING(RAND() * 900 + 100);`
- 生成 4 位的随机数 `SELECT CEILING(RAND() * 9000 + 1000);`
- 生成 5 位的随机数 `SELECT CEILING(RAND() * 90000 + 10000);`

```
select rand() from dual;
echo: 0.5969879996003471
```
