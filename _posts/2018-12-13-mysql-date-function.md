---
layout:       post
title:        "Mysql日期时间函数汇总"
subtitle:     "常用的mysql时间函数记录"
date:         2018-12-13 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-28.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - mysql
---

# 前言
写sql的时候，经常需要处理一些日期时间格式或者差值计算等，有些常用的函数记录下，以备后面用到。

# 时间差函数

#### 常用表达式
<table>
<thead>
<tr>
  <th>格式</th>
  <th>描述</th>
</tr>
</thead>
<tbody><tr>
  <td>%a</td>
  <td>缩写星期名</td>
</tr>
<tr>
  <td>%b</td>
  <td>缩写月名</td>
</tr>
<tr>
  <td>%c</td>
  <td>月，数值</td>
</tr>
<tr>
  <td>%D</td>
  <td>带有英文前缀的月中的天</td>
</tr>
<tr>
  <td><strong>%d</strong></td>
  <td>月的天，数值(00-31)</td>
</tr>
<tr>
  <td>%e</td>
  <td>月的天，数值(0-31)</td>
</tr>
<tr>
  <td>%f</td>
  <td>微秒</td>
</tr>
<tr>
  <td>%H</td>
  <td>小时 (00-23)</td>
</tr>
<tr>
  <td>%h</td>
  <td>小时 (01-12)</td>
</tr>
<tr>
  <td>%I</td>
  <td>小时 (01-12)</td>
</tr>
<tr>
  <td>%i</td>
  <td>分钟，数值(00-59)</td>
</tr>
<tr>
  <td>%j</td>
  <td>年的天 (001-366)</td>
</tr>
<tr>
  <td>%k</td>
  <td>小时 (0-23)</td>
</tr>
<tr>
  <td>%l</td>
  <td>小时 (1-12)</td>
</tr>
<tr>
  <td>%M</td>
  <td>月名</td>
</tr>
<tr>
  <td><strong>%m</strong></td>
  <td>月，数值(00-12)</td>
</tr>
<tr>
  <td>%p</td>
  <td>AM 或 PM</td>
</tr>
<tr>
  <td>%r</td>
  <td>时间，12-小时（hh:mm:ss AM 或 PM）</td>
</tr>
<tr>
  <td>%S</td>
  <td>秒(00-59)</td>
</tr>
<tr>
  <td>%s</td>
  <td>秒(00-59)</td>
</tr>
<tr>
  <td>%T</td>
  <td>时间, 24-小时 (hh:mm:ss)</td>
</tr>
<tr>
  <td>%U</td>
  <td>周 (00-53) 星期日是一周的第一天</td>
</tr>
<tr>
  <td>%u</td>
  <td>周 (00-53) 星期一是一周的第一天</td>
</tr>
<tr>
  <td>%V</td>
  <td>周 (01-53) 星期日是一周的第一天，与 %X 使用</td>
</tr>
<tr>
  <td>%v</td>
  <td>周 (01-53) 星期一是一周的第一天，与 %x 使用</td>
</tr>
<tr>
  <td>%W</td>
  <td>星期名</td>
</tr>
<tr>
  <td>%w</td>
  <td>周的天 （0=星期日, 6=星期六）</td>
</tr>
<tr>
  <td>%X</td>
  <td>年，其中的星期日是周的第一天，4 位，与 %V 使用</td>
</tr>
<tr>
  <td>%x</td>
  <td>年，其中的星期一是周的第一天，4 位，与 %v 使用</td>
</tr>
<tr>
  <td><strong>%Y</strong></td>
  <td>年，4 位</td>
</tr>
<tr>
  <td>%y</td>
  <td>年，2 位</td>
</tr>
</tbody></table>

- 年月日时分秒：%Y-%m-%d %H:%i:%s
- 年月日：%Y-%m-%d
- 时分秒：%T

#### DATE_FORMAT
{% highlight sql %}
SELECT DATE_FORMAT(NOW(),'%Y-%m-%d %H:%i:%s');
{% endhighlight %}

#### STR_TO_DATE
{% highlight sql %}
SELECT STR_TO_DATE('2018-12-11', '%Y-%m-%d');
SELECT STR_TO_DATE('2018-12-11 23:59:59', '%Y-%m-%d %H:%i:%s');
{% endhighlight %}

#### TIMESTAMPDIFF

> TIMESTAMPDIFF函数，有参数设置，可以精确到天（DAY）、小时（HOUR），分钟（MINUTE）和秒（SECOND），使用起来比datediff函数更加灵活。对于比较的两个时间，时间小的放在前面，时间大的放在后面。

> 语法：TIMESTAMPDIFF(interval, datetime_expr1, datetime_expr2)

> 结果：返回（时间2-时间1）的时间差，结果单位由interval参数给出。

**DEMO**

{% highlight sql %}
# 毫秒（低版本不支持，用second，再除于1000）[MySQL 5.6之后才支持毫秒的记录和计算]
SELECT TIMESTAMPDIFF(FRAC_SECOND,'2012-10-01','2013-01-13');# 8985600
# 秒
SELECT TIMESTAMPDIFF(SECOND,'2012-10-01','2013-01-13');# 8985600
# 分
SELECT TIMESTAMPDIFF(MINUTE,'2012-10-01','2013-01-13'); # 149760
# 时
SELECT TIMESTAMPDIFF(HOUR,'2012-10-01','2013-01-13'); # 2496
# 天
SELECT TIMESTAMPDIFF(DAY,'2012-10-01','2013-01-13'); # 104
# 周
SELECT TIMESTAMPDIFF(WEEK,'2012-10-01','2013-01-13'); # 14
# 月
SELECT TIMESTAMPDIFF(MONTH,'2012-10-01','2013-01-13'); # 3
# 季度
SELECT TIMESTAMPDIFF(QUARTER,'2012-10-01','2013-01-13'); # 1
# 年
SELECT TIMESTAMPDIFF(YEAR,'2012-10-01','2013-01-13'); # 0
{% endhighlight %}

#### DATEDIFF

> datediff函数，返回值是相差的天数，不能定位到小时、分钟和秒。

> datediff(datetime_expr1, datetime_expr2)

> 传入两个日期参数，比较DAY天数，第一个参数减去第二个参数的天数值。

{% highlight sql %}
SELECT DATEDIFF(now(), now());

SELECT DATEDIFF('2015-04-22 23:59:59', '2015-04-20 00:00:00');

SELECT DATEDIFF('2015-04-22 00:00:00', '2015-04-20 23:59:00');
{% endhighlight %}

#### TIMESTAMPADD
> TIMESTAMPADD(interval,int_expr,datetime_expr)

> 将整型表达式int_expr 添加到日期或日期时间表达式 datetime_expr中。式中的interval和上文中列举的取值是一样的。

> 可用于时间增量

{% highlight sql %}
SELECT TIMESTAMPADD(YEAR,1,'2018-12-11 09:00:00');
SELECT TIMESTAMPADD(QUARTER,1,'2018-12-11 09:00:00');
SELECT TIMESTAMPADD(MONTH,1,'2018-12-11 09:00:00');
SELECT TIMESTAMPADD(WEEK,1,'2018-12-11 09:00:00');
SELECT TIMESTAMPADD(DAY,1,'2018-12-11 09:00:00');
SELECT TIMESTAMPADD(HOUR,1,'2018-12-11 09:00:00');
SELECT TIMESTAMPADD(MINUTE,60,'2018-12-11 09:00:00');
SELECT TIMESTAMPADD(SECOND,60,'2018-12-11 09:00:00');
{% endhighlight %}

#### DATE_ADD
> 日期加法

> DATE_ADD(date,INTERVAL expr type)

{% highlight sql %}
SELECT DATE_ADD(NOW(), INTERVAL 2 YEAR);
SELECT DATE_ADD(NOW(), INTERVAL 2 QUARTER);
SELECT DATE_ADD(NOW(), INTERVAL 2 MONTH);
SELECT DATE_ADD(NOW(), INTERVAL 2 DAY);
SELECT DATE_ADD(NOW(), INTERVAL 2 HOUR);
SELECT DATE_ADD(NOW(), INTERVAL 2 WEEK);
SELECT DATE_ADD(NOW(), INTERVAL 2 MINUTE);
SELECT DATE_ADD(NOW(), INTERVAL 2 SECOND);
SELECT DATE_ADD(NOW(), INTERVAL 2 MICROSECOND);
{% endhighlight %}

#### DATE_SUB
> 日期减法

> DATE_SUB(date,INTERVAL expr type)

{% highlight sql %}
SELECT DATE_SUB(NOW(), INTERVAL 2 YEAR);
SELECT DATE_SUB(NOW(), INTERVAL 2 QUARTER);
SELECT DATE_SUB(NOW(), INTERVAL 2 MONTH);
SELECT DATE_SUB(NOW(), INTERVAL 2 DAY);
SELECT DATE_SUB(NOW(), INTERVAL 2 HOUR);
SELECT DATE_SUB(NOW(), INTERVAL 2 WEEK);
SELECT DATE_SUB(NOW(), INTERVAL 2 MINUTE);
SELECT DATE_SUB(NOW(), INTERVAL 2 SECOND);
SELECT DATE_SUB(NOW(), INTERVAL 2 MICROSECOND);
{% endhighlight %}

#### EXTRACT(unit  FROM  date)

> 从日期中抽取出某个单独的部分或组合

- SELECT NOW(), extract(YEAR FROM NOW()); -- 年
- SELECT NOW(), extract(QUARTER FROM NOW()); -- 季度
- SELECT NOW(), extract(MONTH FROM NOW()); -- 月
- SELECT NOW(), extract(WEEK FROM NOW()); -- 周
- SELECT NOW(), extract(DAY FROM NOW()); -- 日
- SELECT NOW(), extract(HOUR FROM NOW()); -- 小时
- SELECT NOW(), extract(MINUTE FROM NOW()); -- 分钟
- SELECT NOW(), extract(SECOND FROM NOW()); -- 秒
- SELECT NOW(), extract(YEAR_MONTH FROM NOW()); -- 年月
- SELECT NOW(), extract(HOUR_MINUTE FROM NOW()); -- 时分

# 常用组合函数
{% highlight sql %}
-- 用日期与字符串转换，计算当月第一天、下月第一天
SELECT
    CURDATE() AS '当前日期',
    DATE_FORMAT(CURDATE(), '%Y-%m') AS '当前月份',
    STR_TO_DATE(CONCAT(DATE_FORMAT(CURDATE(), '%Y-%M'), '-01'), '%Y-%M-%D') AS '当前月的第一天',
    DATE_ADD(STR_TO_DATE(CONCAT(DATE_FORMAT(CURDATE(), '%Y-%M'), '-01'), '%Y-%M-%D'), INTERVAL 1 MONTH) AS '下月的第一天';

-- 当前月的最后一天
SELECT LAST_DAY(CURDATE());

-- 下月第一天
SELECT DATE_ADD(LAST_DAY(CURDATE()), INTERVAL 1 DAY);

-- 当天为当月的第几天
SELECT DAY(CURDATE());

-- 当月第一天
SELECT DATE_ADD(CURDATE(), INTERVAL 1-(DAY(CURDATE())) DAY);
{% endhighlight %}

# 日期是第几天？

- DAYOFWEEK(date)：一周内第几天
- DAYOFMONTH(date)：一个月内第几天
- DAYOFYEAR(date)：一年内第几天

{% highlight sql %}
SELECT DAYOFWEEK(NOW());
SELECT DAYOFMONTH(NOW());
SELECT DAYOFYEAR(NOW());
{% endhighlight %}

- DAYNAME(date)：日期是周几的名称
- MONTHNAME(date)：日期是第几月的名称

{% highlight sql %}
SELECT DAYNAME(NOW());
SELECT MONTHNAME(NOW());
{% endhighlight %}

# 其他日期函数

- SELECT NOW(); # 返回当前的日期和时间
- SELECT CURDATE(); # 返回当前日期
- SELECT SYSDATE(); # 返回当前日期
- SELECT CURTIME(); # 返回当前时间
- SELECT CURRENT_TIME(); # 返回当前时间
- SELECT CURRENT_TIMESTAMP; # 返回当前时间
- SELECT CURRENT_TIMESTAMP(); # 返回当前时间
- SELECT UNIX_TIMESTAMP(); # 返回当前时间戳
- SELECT FROM_UNIXTIME(UNIX_TIMESTAMP()); # 返回UNIX时间戳的日期值
- SELECT YEAR(NOW()); # 返回日期date的年份
- SELECT QUARTER(NOW()); # 返回日期date的年份
- SELECT MONTH(NOW()); # 返回日期date的月份
- SELECT MONTHNAME(NOW()); # 返回日期date的月份名
- SELECT DAY(NOW()); # 返回当前日期的天数
- SELECT DATE(NOW()); # 将当前时间转换为当前日期
- SELECT TIME(NOW()); # 将当前时间转换为当前时分秒
- SELECT WEEK(NOW()); # 返回日期date为一年中的第几周
- SELECT HOUR(NOW()); # 返回日期date的小时
- SELECT MINUTE(NOW()); # 返回日期date的分钟
- SELECT SECOND(NOW()); # 返回日期date的秒
- SELECT MICROSECOND(NOW()); # 返回日期date的微秒
- SELECT DATE_FORMAT(NOW(), '%Y-%m-%d'); # 返回按字符串格式化的date值
- LAST_DAY(NOW()); # 返回date所在月最后一天，如果参数无效，返回NULL

**注意：**

now()与sysdate()类似，只不过now()在执行开始时就获取，而sysdate()可以在函数执行时动态获取。


