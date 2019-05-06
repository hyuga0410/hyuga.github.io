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

```js
public class CoordinateTransformUtil {

    private CoordinateTransformUtil() {
    }

    private static Double EARTHRADIUS = 6370996.81;
    private static Double[] MCBAND = {12890594.86, 8362377.87, 5591021d, 3481989.83, 1678043.12, 0d};
    private static Double[] LLBAND = {75d, 60d, 45d, 30d, 15d, 0d};
    
    private static Double[][] MC2LL = {{1.410526172116255e-8, 0.00000898305509648872, -1.9939833816331, 200.9824383106796, -187.2403703815547, 91.6087516669843, -23.38765649603339, 2.57121317296198
            , -0.03801003308653, 17337981.2}, {-7.435856389565537e-9, 0.000008983055097726239, -0.78625201886289, 96.32687599759846, -1.85204757529826, -59.36935905485877, 47.40033549296737,
            -16.50741931063887, 2.28786674699375, 10260144.86}, {-3.030883460898826e-8, 0.00000898305509983578, 0.30071316287616, 59.74293618442277, 7.357984074871, -25.38371002664745,
            13.45380521110908, -3.29883767235584, 0.32710905363475, 6856817.37}, {-1.981981304930552e-8, 0.000008983055099779535, 0.03278182852591, 40.31678527705744, 0.65659298677277,
            -4.44255534477492, 0.85341911805263, 0.12923347998204, -0.04625736007561, 4482777.06}, {3.09191371068437e-9, 0.000008983055096812155, 0.00006995724062, 23.10934304144901,
            -0.00023663490511, -0.6321817810242, -0.00663494467273, 0.03430082397953, -0.00466043876332, 2555164.4}, {2.890871144776878e-9, 0.000008983055095805407, -3.068298e-8, 7.47137025468032,
            -0.00000353937994, -0.02145144861037, -0.00001234426596, 0.00010322952773, -0.00000323890364, 826088.5}};
    private static Double[][] LL2MC = {{-0.0015702102444, 111320.7020616939, 1704480524535203d, -10338987376042340d, 26112667856603880d, -35149669176653700d, 26595700718403920d, -10725012454188240d
            , 1800819912950474d, 82.5}, {0.0008277824516172526, 111320.7020463578, 647795574.6671607, -4082003173.641316, 10774905663.51142, -15171875531.51559, 12053065338.62167,
            -5124939663.577472, 913311935.9512032, 67.5}, {0.00337398766765, 111320.7020202162, 4481351.045890365, -23393751.19931662, 79682215.47186455, -115964993.2797253, 97236711.15602145,
            -43661946.33752821, 8477230.501135234, 52.5}, {0.00220636496208, 111320.7020209128, 51751.86112841131, 3796837.749470245, 992013.7397791013, -1221952.21711287, 1340652.697009075,
            -620943.6990984312, 144416.9293806241, 37.5}, {-0.0003441963504368392, 111320.7020576856, 278.2353980772752, 2485758.690035394, 6070.750963243378, 54821.18345352118, 9540.606633304236,
            -2710.55326746645, 1405.483844121726, 22.5}, {-0.0003218135878613132, 111320.7020701615, 0.00369383431289, 823725.6402795718, 0.46104986909093, 2351.343141331292, 1.58060784298199,
            8.77738589078284, 0.37238884252424, 7.45}};
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

