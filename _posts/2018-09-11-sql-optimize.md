---
layout:       post
title:        "MySQL的一些小优化"
subtitle:     "一些优化建议和sql执行计划"
date:         2018-09-11 22:42:05
author:       "Hyuga"
header-img:   "img/2018/2018-09/head-top-4.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - mysql
---

## 建立索引
- 多表的关联字段是否未建立索引
- 为搜索字段建立索引

## 避免全表扫描
where语句中以下几种语句用法会放弃索引，使用全表扫描
- `like '%xx%'`
    - 优化：`like 'xx%'`
- `!=`和`<>`
- `or`
    - 优化：使用union all拼接
- `in`和`not in`
    - 优化：如果是连续数值，使用between ... in ...代替
    - 优化：not in可以使用not exists代替，如果not exists在子查询中，还可以转换成left、right、inner join
- `is null`和`is not null`
    - 优化：字段默认值赋值0
- 不要在where字段上做运算函数处理

## 强制使用索引
{% highlight java %}
<!--强制使用主键-->
select * from table force index(PRI) limit 1;
<!--强制使用索引"hyuga_index"-->
select * from table force index(hyuga_index) limit 1;
<!--强制使用索引"PRI和hyuga_index"-->
select * from table force index(PRI,hyuga_index) limit 1;
{% endhighlight %}

## 禁止使用索引
{% highlight java %}
<!--禁止使用主键-->
select * from table ignore index(PRI) limit 1;
<!--禁止使用索引"hyuga_index"-->
select * from table ignore index(hyuga_index) limit 1;
<!--禁止使用索引"PRI,hyuga_index"-->
select * from table ignore index(PRI,hyuga_index) limit 1;
{% endhighlight %}

## 简单语句
- 一条sql越简单越好，能不做其他操作就不做其他操作
- 不要使用select *，用不到的字段不要返回
- 关联查询表不要太多，如果太多需要考虑是否数据层级关联有问题
- `order by`后面不要使用函数，如`rand( )`等

## insert批量插入
{% highlight sql %}
insert into person(name,age) values(‘x1’, 1);
insert into person(name,age) values(‘x2’, 2);
insert into person(name,age) values(‘x3’, 3);
优化：
insert into person(name,age) values(‘x1’, 1), (‘x2’, 2),(‘x3’, 3);

mybatis语法：
insert into data_comment(FId, FSiteId, FContent, FIsCover)
values
<foreach collection="comments" item="c" index="index" separator=",">
  (#{c.id},#{c.siteId},#{c.content},#{c.isCover})
</foreach>
{% endhighlight %}

## EXISTS和NOT EXISTS
语法：`SELECT … FROM table WHERE EXISTS (subquery)`

语义：将主查询的数据，放到子查询中做条件验证，根据验证结果(true or false)来决定主查询的数据是否得以保存。
{% highlight sql %}
select id,name from company where exists (select * from product where comid=company.id);
等价于：
select id,name from company where id in(select comid from product);

select id,name from company where not exists (select * from product where comid=company.id);
等价于：
select id,name from company where id not in(select comid from product);
{% endhighlight %}

## union和union all
主要差异是前者将多个结果集合并后再进行唯一性过滤，数据集重排序，增加cpu资源消耗和延迟。
当确定主需要做数据合并返回的时候，一定要用union all.

## 大数据分页优化
使用between #{start} and #{end}代替limit #{startIndex},#{pageSize}

## 注意
- 并不是所有索引对查询都有效，SQL是根据表中数据来进行查询优化的，当索引列有大量数据重复时，SQL查询可能不会去利用索引。
- 索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert 及 update 的效率。
- 尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。不要设计个char或者varchar去存储int型数据。
- 尽可能的使用 varchar/nvarchar 代替 char/nchar
- 尽量避免使用游标。
- 尽量避免使用表达式、函数等操作作为查询条件。
- 尽量避免大事务操作，提高系统并发能力。

## 场景解析
**left join、right join、inner join效率？**

left和right都是外链接，效率不及inner join，当需要等值连接的时候，一定要用inner join。

下面这条语句其实也就是隐式使用了inner join
{% highlight sql %}
SELECT A.id,A.name,B.id,B.name FROM A,B WHERE A.id = B.id;
{% endhighlight %}

**用外连接还是子查询？**

子查询会先将主表进行全表查询，然后再根据条件逐步执行子查询语句。如果主表是一张很大的表会严重影响性能。
能用inner join替代的就一定要用inner。

当需要用left或者right代替的话，要考虑实际情况，如果子查询条件只在极少数的场景中触发，则不要在主表上left、right从表，造成每次查询都要关联从表，又不对从表做任何操作。

## explain执行计划详解
{% highlight sql %}
explain select * from t_site_reservation t
left join t_outreach_broker b on b.FID = t.FKEmpId
where exists(select 1 from t_site_reservation r where t.FReserveTelNo like '135%');
{% endhighlight %}
执行结果:

![](/img/2018/2018-09/explain-1.png)
下面逐步分析执行过程！！！

mysql执行计划结果有10列，每一列有各自的含义。

#### id
> 由一组数字组成。表示一个查询中各个子查询的执行顺序。

- id相同执行顺序由上至下。
- id不同，id值越大优先级越高，越先被执行。
- id为null时表示一个结果集，不需要使用它查询，常出现在包含union等查询语句中。
```
可见上面例子执行顺序从上往下，1-1-2
```

#### select_type
> 每个子查询的查询类型，一些常见的查询类型。

- SIMPLE：不包含任何子查询或union等查询。
- PRIMARY：包含子查询最外层查询就显示为 PRIMARY。
- SUBQUERY：在select或 where字句中包含的查询。
- DERIVED：from字句中包含的查询。
- UNION：出现在union后的查询语句中。
- UNION RESULT：从UNION中获取结果集。

```
select * from t_site_reservation 是 PRIMARY
left join t_outreach_broker b on b.FID = t.FKEmpId 也是 PRIMARY
where exists(select 1 from t_site_reservation r where t.FReserveTelNo like '135%'); 是 SUBQUERY
没毛病！！！
```

#### table
> 这个显而易见就是操作表

- 如果查询使用了别名，那么这里显示的是别名。
- 如果不涉及对数据表的操作，那么这显示为null。
- 如果显示为尖括号括起来的<derived N>就表示这个是临时表，后边的N就是执行计划中的id，表示结果来自于这个查询产生。
- 如果是尖括号括起来的<union M,N>，与<derived N>类似，也是一个临时表，表示这个结果来自于union查询的id为M,N的结果集。
```
上面sql三张表都用了别名，所以显示t-b-r
```

#### type
> 访问类型

- ALL：扫描全表数据
- index：遍历索引。
- range：索引范围查找。
- index_subquery：在子查询中使用 ref。
- unique_subquery：在子查询中使用 eq_ref。
- ref_or_null：对Null进行索引的优化的 ref。
- fulltext：使用全文索引。
- ref：使用非唯一索引查找数据。
- eq_ref：在join查询中使用PRIMARY KEY or UNIQUE NOT NULL索引关联。
- const：使用主键或者唯一索引，且匹配的结果只有一条记录。
- system const：连接类型的特例，查询的表为系统表。

性能排名从好到差：system，const，eq_ref，ref，fulltext，ref_or_null，unique_subquery，index_subquery，range，index_merge，index，ALL
除了ALL之外，其他的type都可以使用到索引，除了index_merge之外，其他的type只可以用到一个索引。

如果通过执行计划发现某张表的查询语句的type显示为ALL，那就要考虑添加索引，或者更换查询方式，使用索引进行查询。

```
第一条是ALL扫描了全表，第二条eq_ref在jion中使用主键或唯一索引，第三条index使用了遍历索引。
```

#### possible_keys
> 可能使用的索引，但不一定会使用。

- 查询涉及到的字段上若存在索引，则该索引将被列出来。
- 当该列为 NULL时就要考虑当前的SQL是否需要优化了。

    第二条on b.FID = t.FKEmpId，存在两个索引，sql执行中可能会用到。

#### key
> 显示MySQL在查询中实际使用的索引，若没有使用索引，显示为NULL。

- TIPS:查询中若使用了覆盖索引(覆盖索引：索引的数据覆盖了需要查询的所有数据)，则该索引仅出现在key列表中。
- select_type为index_merge时，这里可能出现两个以上的索引，其他的select_type这里只会出现一个。

```
第一条null，没有执行任何索引
第二条执行了主键索引b.FID
第三条执行了T_SITE_RESERVATION_FKCUSTOMEID，这里不太理解怎么会执行这个？！
```

#### key_length
- 索引长度 char()、varchar()索引长度的计算公式：
- (Character Set：utf8mb4=4,utf8=3,gbk=2,latin1=1) * 列长度 + 1(允许null) + 2(变长列)

#### ref
> 表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值

- 如果是使用的常数等值查询，这里会显示const。
- 如果是连接查询，被驱动表的执行计划这里会显示驱动表的关联字段。
- 如果是条件使用了表达式或者函数，或者条件列发生了内部隐式转换，这里可能显示为func。

#### rows
> 返回估算的结果集数目，注意这并不是一个准确值。

#### extra
> extra的信息非常丰富，常见的有

- Using index 使用覆盖索引。
- Using where 使用了用where子句来过滤结果集。
- Using filesort 使用文件排序，使用非索引列进行排序时出现，非常消耗性能，尽量优化。
- Using temporary 使用了临时表。

```
第一条使用了where子句过滤，第二条null，第三条使用了where子句过滤和覆盖索引。
```

## 优化步骤
- 先从业务入手，看是否有更高效的关联或者移除无效表关联和无用的返回字段
- 查看是否未创建必要的索引，或者是否用错索引，改用组合索引或使用强制索引
- 剖析sql复杂度，简化sql，通过语法优化或者通过代码组合优化
- 通过执行计划分析sql执行过程，分析优化

## 参考资料
[教你如何定位及优化SQL语句的性能问题-Hollis](https://mp.weixin.qq.com/s?__biz=MzI3NzE0NjcwMg==&mid=2650122000&idx=1&sn=3d8e924fb28473649ef620e07a96bfb2&chksm=f36bba31c41c3327c43845e61a9b17a9ff03e28a0a36d0e09f09e3bbfc21f443db0174a6a9f6&scene=21#wechat_redirect)