---
layout:       post
title:        "百度地图坐标转经纬度"
subtitle:     "百度地图获取坐标转换为常规使用的经纬度坐标"
date:         2019-04-28 18:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-15.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - util
---

# 前言
近期有需求需要抓取深圳的所有地铁线路和站点，从百度地图下手。

找到api后抓取数据存储。发现百度地图返回的x,y坐标不是平时使用的经纬度坐标，需要进行转换。

尝试了很多方案搞不定后，有幸在网上找到对应的转换工具类。

一时没能想起是从哪个博主处抄来的，如果侵权，请联系处理，谢谢。

# 工具类

```
public class CoordinateTransformUtil {

    private CoordinateTransformUtil() {
    }

    private static Double EARTHRADIUS = 6370996.81;
    private static Double[] MCBAND = `{12890594.86, 8362377.87, 5591021d, 3481989.83, 1678043.12, 0d}`;
    private static Double[] LLBAND = {75d, 60d, 45d, 30d, 15d, 0d};
    
    private static Double[][] MC2LL = {{}};//具体代码这里编译不通过，就不贴了
    private static Double[][] LL2MC = {{}};
    private static double x_pi = 3.14159265358979324 * 3000.0 / 180.0;

    /**
     * 墨卡托坐标转经纬度坐标
     *
     * @param x x
     * @param y y
     * @return 经纬度坐标
     */
    public static LngLat convertMC2LL(Double x, Double y) {
        Double[] cF = null;
        x = Math.abs(x);
        y = Math.abs(y);
        for (int cE = 0; cE < MCBAND.length; cE++) {
            if (y >= MCBAND[cE]) {
                cF = MC2LL[cE];
                break;
            }
        }
        assert cF != null;
        Map<String, Double> location = converter(x, y, cF);
        return new LngLat(location.get("x"), location.get("y"));
    }

    private static Map<String, Double> converter(Double x, Double y, Double[] cE) {
        double xTemp = cE[0] + cE[1] * Math.abs(x);
        Double cC = Math.abs(y) / cE[9];
        double yTemp = cE[2] + cE[3] * cC + cE[4] * cC * cC + cE[5] * cC * cC * cC + cE[6] * cC * cC * cC * cC + cE[7] * cC * cC * cC * cC * cC + cE[8] * cC * cC * cC * cC * cC * cC;
        xTemp *= (x < 0 ? -1 : 1);
        yTemp *= (y < 0 ? -1 : 1);
        Map<String, Double> location = new HashMap<>(16);
        location.put("x", xTemp);
        location.put("y", yTemp);
        return location;
    }

    /**
     * 对double类型数据保留小数点后多少位
     * 高德地图转码返回的就是 小数点后6位，为了统一封装一下
     *
     * @param digit 位数
     * @param in    输入
     * @return 保留小数位后的数
     */
    static double dataDigit(int digit, double in) {
        return new BigDecimal(in).setScale(6, BigDecimal.ROUND_HALF_UP).doubleValue();
    }

    /**
     * 将火星坐标转变成百度坐标
     *
     * @param longitudeGd 火星坐标（高德、腾讯地图坐标等）
     * @return 百度坐标
     */

    public static LngLat bdEncrypt(LngLat longitudeGd) {
        double x = longitudeGd.getLongitude(), y = longitudeGd.getLatitude();
        double z = sqrt(x * x + y * y) + 0.00002 * sin(y * x_pi);
        double theta = atan2(y, x) + 0.000003 * cos(x * x_pi);
        return new LngLat(dataDigit(6, z * cos(theta) + 0.0065), dataDigit(6, z * sin(theta) + 0.006));
    }

    /**
     * 将百度坐标转变成火星坐标
     *
     * @param longitudeBd 百度坐标（百度地图坐标）
     * @return 火星坐标(高德 、 腾讯地图等)
     */
    private static LngLat bdDecrypt(LngLat longitudeBd) {
        double x = longitudeBd.getLongitude() - 0.0065, y = longitudeBd.getLatitude() - 0.006;
        double z = sqrt(x * x + y * y) - 0.00002 * sin(y * x_pi);
        double theta = atan2(y, x) - 0.000003 * cos(x * x_pi);
        return new LngLat(dataDigit(6, z * cos(theta)), dataDigit(6, z * sin(theta)));
    }
}
```

