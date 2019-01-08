---
layout:       post
title:        "坐标计算工具类"
subtitle:     "经纬度范围计算小工具"
date:         2019-01-08 22:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-30.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - util
---

# 前言

需求：根据地铁站坐标计算出附近几公里内的楼盘信息。

有种做法是将坐标放到sql中做计算，和表中所有数据的坐标计算下距离是否符合条件，筛选出符合条件的楼盘信息。
- 优点：计算出来的数据会更准确。
- 弊端：sql做一堆函数计算，复杂度高，执行效率低。

还有一种做法是现在代码中计算出该坐标多少公里的范围坐标值，传入sql中拼接查询。
- 优点：sql相对简单很多，没有复杂的函数计算，执行效率高。
- 弊端：代码计算出来的范围值是一个包含了圆形的正方形范围值，sql查出的数据会有些许偏差。

由于需求要求精度不是很高，多方面考虑，选第二种方式实现功能。

# 实现
考虑到复用，把这种计算作为一个工具类较为合适。

`CoordinateCalculationUtil` 坐标计算工具类

{% highlight java %}
/**
 * 坐标计算
 *
 * @author hyuga
 * @date 2019-01-08 15:15
 */
public class CoordinateCalculationUtil {

    private CoordinateCalculationUtil() {
    }

    /**
     * 地球半径
     */
    private static final double EARTH_RADIUS = 6371.393;

    /**
     * 根据经纬计算指定公里范围经纬的最大最小值
     *
     * @param longitude 经度
     * @param latitude  纬度
     * @param kilometer 多少公里
     * @return 经纬度范围对象
     */
    public static LatitudeAndLongitudeRange getRange(double longitude, double latitude, int kilometer) {
        //里面的 1 就代表搜索 1km 之内，单位km
        final double range = 180 / Math.PI * kilometer / EARTH_RADIUS;
        final double lngR = range / Math.cos(latitude * Math.PI / 180);
        //最大纬度
        double maxLat = latitude + range;
        //最小纬度
        double minLat = latitude - range;
        //最大经度
        double maxLng = longitude + lngR;
        //最小经度
        double minLng = longitude - lngR;

        LatitudeAndLongitudeRange rangeBean = new LatitudeAndLongitudeRange();
        rangeBean.setMinLongitude(minLng);
        rangeBean.setMaxLongitude(maxLng);
        rangeBean.setMinLatitude(minLat);
        rangeBean.setMaxLatitude(maxLat);

        return rangeBean;
    }

    /**
     * 根据经纬计算指定公里范围经纬的最大最小值
     *
     * @param latitudeAndLongitude 经纬度对象
     * @param kilometer            多少公里
     * @return 经纬度范围对象
     */
    public static LatitudeAndLongitudeRange getRange(LatitudeAndLongitude latitudeAndLongitude, int kilometer) {
        return getRange(latitudeAndLongitude.getLongitude(), latitudeAndLongitude.getLatitude(), kilometer);
    }

    /**
     * 根据经纬计算指定公里范围经纬的最大最小值
     *
     * @param latitudeAndLongitudes 经纬度对象集合
     * @param kilometer             多少公里
     * @return 经纬度范围对象集合
     */
    public static List<LatitudeAndLongitudeRange> queryRange(List<LatitudeAndLongitude> latitudeAndLongitudes, int kilometer) {
        if (ListUtil.isEmpty(latitudeAndLongitudes)) {
            return ListUtil.emptyList();
        }
        List<LatitudeAndLongitudeRange> result = ListUtil.NEW();
        latitudeAndLongitudes.forEach(latitudeAndLongitude -> result.add(getRange(latitudeAndLongitude, kilometer)));
        return result;
    }
}
{% endhighlight %}

- 支持单个坐标获取最大最小经纬度范围
- 支持多个坐标获取最大最小经纬度范围集合

得到最大最小经纬度范围集合后，传入sql中进行查询！

```
select *
        from t_garden_basic t
        where
          (t.flongitude >= '114.01586699540523' and t.flongitude <= '114.11323700459478'
           and t.flatitude >= '22.503492693299975' and t.flatitude <= '22.593419306700028')

          or (t.flongitude >= '114.01586699540523' and t.flongitude <= '114.11323700459478'
              and t.flatitude >= '22.503492693299975' and t.flatitude <= '22.593419306700028')

          or (t.flongitude >= '114.01586699540523' and t.flongitude <= '114.11323700459478'
              and t.flatitude >= '22.503492693299975' and t.flatitude <= '22.593419306700028');
```

这种方式也许不是当前场景下的最优方案，但相对于第一种，也算是比较符合的了。

附带贴下第一种方式的实现逻辑：

```
赤道半径：6378.137km
查询结果为km
SELECT id,(
6378.137 * 2 * ASIN(
    SQRT(
        POW(
            SIN(
                (
                    RADIANS(当前纬度latitude)- RADIANS(数据库中存储的目标纬度latitude)
                )/ 2
            ),
            2
        )+ COS(RADIANS(当前纬度latitude))* COS(RADIANS(数据库中存储的目标纬度latitude))* POW(
            SIN(
                (
                    RADIANS(当前经度longitude)- RADIANS(数据库中存储的目标经度longitude)
                )/ 2
            ),
            2
        )
    )
)
) AS distance FROM tablename
```

以上实现来自：[Mysql sql 计算两个坐标之间的距离](https://crabdave.iteye.com/blog/2301497)

# 参考资料
[Java,Mysql-根据一个给定经纬度的点，进行附近500米地点查询–合理利用算法](https://www.cnblogs.com/zt007/p/6373722.html)

![](https://images2015.cnblogs.com/blog/1034021/201702/1034021-20170207133355432-51913608.png)
