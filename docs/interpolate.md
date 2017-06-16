# 插值器

参考文章：[d3-interpolate](https://github.com/d3/d3-interpolate/blob/master/README.md)

## 线性插值器

使用方式：

interpolate(a, b)(t) = a + b * t;

注：为了性能考虑，Date，Array，Object插值器
每次返回的结果公用同一个对象。

|类型|备注|
|:--|:--|
|interpolate|依据入参类型使用下方不同的插值器|
|interpolateNumber|数值型|
|interpolateRound|数值型，结果取整|
|interpolateString|会根据a,b中的数字进行线性插值，其余文本保留b中的|
|interpolateDate|*	日期型|
|interpolateArray|*	a的长度必须不超过b的长度。每个位置的数据分别计算插值，b中多出来的保留|
|interpolateObject|*	类似Array|
|interpolateTransformCss|以css的transform方式来解析参数|
|interpolateTransformSvg|类似css，按照svg的语法|

## 空间移动

- interpolateZoom(a, b)

a和b的格式均为[ux, uy, w]，表示视图中心和尺寸。返回的插值器有一种弹跳效果（w会超出a,b的范围）。
可用于做动画，返回的插值器同时会包含duration属性，表示推荐以毫秒计的动画持续时间。

## 取样

- quantize(interpolator, n)

返回`[interpolator(i) where i in [0, n)]`

## 颜色空间

|类型|备注|
|:--|:--|
|interpolateRgb|可设置gamma|
|interpolateRgbBasis|参数为颜色数组，使用B样条插值|
|interpolateRgbBasisClosed|类似上方，但首尾颜色连续|
|interpolateHsl||
|interpolateHslLong||
|interpolateLab||
|interpolateHcl||
|interpolateHclLong||
|interpolateCubehelix|可设置gamma|
|interpolateCubehelixLong|可设置gamma|

## 样条

目前只支持B样条插值

- interpolateBasis(values)
- interpolateBasisClosed(values)
