参考文章：[d3-scale](https://github.com/d3/d3-scale/blob/master/README.md)

|类型|domain定义域|range值域|分类|备注|
|:--|:--|:--|:--|:--|
|linear|连续|连续|连续Continuous|domain和range区间数要求一样，每一段都是线性的，计算公式y = a * x + b|
|pow|连续|连续|连续|类似linear，可以设置指数exponent，计算公式y = mx^k + b。特别地，当exponent为0.5时，称为sqrt比例尺|
|log|连续，但不能包括0|连续|连续|可以设置指数base（默认10），计算公式y = m log(x) + b|
|identity|连续|连续|连续|恒等比例尺|
|time|连续的时间，有起止Date对象指定|连续|连续|ticks可指定时间间隔。当时间为utc时间是，又称为utc比例尺|
|sequential|连续|连续，但不能指定|顺序Sequential|range根据interpolator插值器从domain计算得出，常用于颜色，参考d3-scale-chromatic。自带viridis/inferno/magma/plasma/warn/cool/rainbow/cubehelixDefault插值器|
|quantize|连续|离散值列表|量化quantize|根据range数量把domain分成等分片段，每个片段对应一个range中的值。计算公式y = m round(x) + b|
|quantile|样本数据|离散值列表|量化quantize|先把domain排序，再根据range的数据以quantile算法得出每个区间对应关系|
|threshold|阈值数组，须有序|离散值列表，值数量比阈值多1|量化quantize|少于第一个阈值的取range第一个值|
|ordinal|离散值列表|离散值列表|序数ordinal|一对一关系，常用语选择图表颜色。scale中自带的颜色range有schemeCategory10，schemeCategory20b，schemeCategory20c，schemeCategory20；更多颜色见[d3-scale-chromatic](https://github.com/d3/d3-scale-chromatic)|
|band|离散值列表|连续|序数ordinal|常用语多系列的柱状图横坐标。range被分为间隔的paddingOuter，bandwidth，paddingInner，并根据align做左右偏移。特别地，当paddingInner为1，bandwidth为0时，称为point比例尺|
