# 图形

参考文章：[d3-shape](https://github.com/d3/d3-shape/blob/master/README.md)

## 使用方法

以line为例。

```js
var line = d3.line()
    .x(function(d) { return x(d.date); })
    .y(function(d) { return y(d.value); });
path.datum(data).attr("d", line);
line.context(context)(data);
```

`line()`调用生成一个线段生成器，可以进行多种设置，`x`和`y`是必须的，
用于从数据中获取x和y的值（一般配合scale比例尺使用）。另外也可以设置别的，
例如线条种类。`context`用于设置绘制上下文，如果设置了，
则调用这个生成器会在这个上下文中绘制（常用于web上的canvas绘图），返回undefined；
如果不设置，则使用d3.path绘制，并且生成path的字符串表示，常用于SVG绘图。

注：

下方所有设置参数的方法，若不传参，则为获取当前参数。

## 提供的图形

### arc 圆弧

- d3.arc()          构建一个新的默认的arc生成器
- arc(arguments…)   根据指定的arguments进行绘制

设置参数

- startAngle    开始角度
- endAngle      结束角度
- innerRadius   设置内部半径
- outerRadius   设置外部半径
- cornerRadius  设置拐角半径
- padAngle      设置饼图里面每个扇形的间隔
- padRadius     和padAngle搭配，指示在哪个半径处间隔角度为padAngle
- context       设置context

以上除了context，都可以在绘制的时候作为参数传入，如：

```js
var arc = d3.arc();
arc({
  innerRadius: 0,
  outerRadius: 100,
  startAngle: 0,
  endAngle: Math.PI / 2
}); // "M0,-100A100,100,0,0,1,100,0L0,0Z"
```

方法

- centroid      计算参数所表示的弧的中间点

### pie 饼图

主要用于计算出一组用于生成pie或圆环的数据集。然后根据计算结果使用arc进行绘制。

- d3.pie()                  构造一个默认的pie生成器。
- pie(data[, arguments…])   根据指定的数组data返回一个对象数组，每个对象都包含一些用于绘制的属性。

pie方法生成的数组元素包含以下属性：

- data          输入的原始数据元素
- value         当前pie的值
- index         基于0的索引
- startAngle    当前pie 的起始角度
- endAngle      当前pie的终止角度
- padAngle      相邻pie之间的间隙

设置参数

- value         设置值访问器，用于处理data数组中的元素
- sortValues    对输入元素进行排序，排序比较对象是进过value方法处理后的值
- sort          对输入元素进行排序，排序比较对象原始data数组中的值
- startAngle    用于设置饼图的起始角度，默认为0
- endAngle      用于设置饼图的结束角度，默认为2Pi
- padAngle      设置相邻pie之间的间隙角度

### line 折线图

- d3.line()     构造一个默认的pie生成器。
- line(data)    根据指定的数组data返回一个对象数组，每个对象都包含一些用于绘制的属性。

设置参数

- x             设置x访问器，即如何从数据中获取x坐标
- y             设置y访问器
- defined       设置判断值是否已定义的访问器。若返回false，该点会形成中断
- curve         设置线的类型（插值方式），例如需要平滑曲线，默认是折线
- context       设置context

### lineRadial/radialLine

类似于line，区别在于是极坐标的，angle对应x，radius对应y

### area 区域图

```js
// x, y 是对应的比例尺
// 下方生成一个绘制底边在x轴的面积图生成器
d3.area()
  .x(d => x(d[0]))
  .y1(d => y(d[1]))
```

area指上下由两条line围成的区域，一般上面的line是x1,y1，下面的是x0,y0。
一般x1和x0取值一样（这是x1为null）；还有一种很常见的情况是y0是常量，如0。

- d3.area()     使用默认的设置构建一个面积图生成器。
- area(data)    根据指定的数据进行绘制

设置参数

- x             将x0设置为x并将x1设置为null（实际效果如x1==x0)
- x0            设置x0，其他类似，略
- x1 
- y 
- y0            默认为返回0
- y1 
- defined 
- curve 
- context 

以下返回线段生成器

- lineY0        由x0/y0组成
- lineY1        由x0/y1组成
- lineX0        由x0/y0组成
- lineX1        由x1/y0组成

### areaRadial/radialArea

类似于line，区别在于是极坐标的，对应关系如下：

- angle => x
- startAngle => x0
- endAngle => x1
- radius => y
- innerRadius => y0
- outerRadius => y1
- lineStartAngle => lineX0
- lineEndAngle => lineX1
- lineInnerRadius => lineY0
- lineOuterRadius => lineY1

## Curves 曲线类型

line和area都有curve方法用于设置如何连接各个点的方法（插值）。
内置的插值算法有以下几种：

- curveBasis            三次B样条曲线（内部调用了bezierCurveTo？）
- curveBasisClosed      首尾相连
- curveBasisOpen        不包含首尾
- curveBundle           基于curveBasis，可设置beta用于描述弯曲程度
- curveCardinal         cardinal样条，可调整张量tension
- curveCardinalClosed   
- curveCardinalOpen     
- curveCatmullRom       Catmull–Rom样条，可设置alpha值
- curveCatmullRomClosed 
- curveCatmullRomOpen   
- curveLinear           线性插值
- curveLinearClosed     
- curveMonotoneX        生成一个在y方向保持单调性的曲线
- curveMonotoneY        生成一个在x方向保持单调性的曲线
- curveNatural          自然的三次曲线
- curveStep             台阶状，y值在两个相邻的点x值中间点发生变化
- curveStepAfter        
- curveStepBefore       

## 自定义曲线样式

要定义一个曲线样式，需要实现以下接口

- areaStart
- areaEnd
- lineStart
- lineEnd
- point

## link 树状连接图

类型

- linkVertical      垂直方向连线
- linkHorizontal    水平方向连线
- linkRadial        类似上方，圆形，数据位于圆周上，angle对应x，radius对应y

## 内置符号

使用方法

```js
var d = d3.symbol()
  .type(d3.symbolCross)
  .size(64)
  ();
```

- type(type)        设置类型
- size(size)        设置面积大小
- context(context)  

内置类型

- symbolCircle
- symbolCross     十字
- symbolDiamond   菱形
- symbolSquare
- symbolStar
- symbolTriangle
- symbolWye       Y字形

## 自定义符号

只需要实现以下方法

- draw(context, size)

## stack 层叠类型

层叠类型不限于一种，可以是柱状图、面积图、折线图等。
与饼图一样，不直接生成具体的图，而是生成对应的数据，由具体的图形来绘制。

- d3.stack() 使用默认的设置构建一个stack生成器。
- stack(data[, arguments...]) 根据指定的data数组计算一个stack布局，返回一个数组

```js
var data = [
  {month: new Date(2015, 0, 1), apples: 3840, bananas: 1920, cherries: 960, dates: 400},
  {month: new Date(2015, 1, 1), apples: 1600, bananas: 1440, cherries: 960, dates: 400},
  {month: new Date(2015, 2, 1), apples:  640, bananas:  960, cherries: 640, dates: 400},
  {month: new Date(2015, 3, 1), apples:  320, bananas:  480, cherries: 640, dates: 400}
];
var stack = d3.stack()
    .keys(["apples", "bananas", "cherries", "dates"])
    .order(d3.stackOrderNone)
    .offset(d3.stackOffsetNone);

var series = stack(data);
/* ==>
[
  [[   0, 3840], [   0, 1600], [   0,  640], [   0,  320]], // apples
  [[3840, 5760], [1600, 3040], [ 640, 1600], [ 320,  800]], // bananas
  [[5760, 6720], [3040, 4000], [1600, 2240], [ 800, 1440]], // cherries
  [[6720, 7120], [4000, 4400], [2240, 2640], [1440, 1840]], // dates
]
每一个二级数组元素，都带有一个data的key，记录原始数据，
每一个一级数组元素，都带有一个key的key，记录所用的key
同时带有一个index的key，表示系列的顺序，参考order
*/
```

设置参数

- keys    一个数组，一个key对应一个类别
- value   函数，指示如何在元素数据中获取值，默认为d[key]
- order   对每个类别进行重排序，默认按keys定义的先后顺序
- offset  

### order类型

- stackOrderNone        默认，按keys定义的顺序
- stackOrderReverse     按keys定义的逆序
- stackOrderAscending   按每个系列的总和升序
- stackOrderDescending  按每个系列的总和降序
- stackOrderInsideOut   最大的值在内侧，最小的值在外侧，这个是针对河流图的

### offset类型

- stackOffsetNone       默认，以0为基线
- stackOffsetExpand     将整个堆叠图放在0-1之间，便于比较不同类别占总体比例
- stackOffsetDiverging  正值堆叠在零以上，而负值堆叠在零以下。
- stackOffsetSilhouette 将基线向下移动，使流线的中心始终为零。
- stackOffsetWiggle     做河流图用的
