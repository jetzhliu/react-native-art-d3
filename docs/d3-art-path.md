# 路径

path的接口与2D画布(canvas)的接口一致，所以可以用于替换面向canvas的接口，
只需要用path实例替换即可。

如：

```js
function drawCircle(context, radius) {
  context.moveTo(radius, 0);
  context.arc(0, 0, radius, 0, 2 * Math.PI);
}
var context = d3.path();
drawCircle(context, 40);
pathElement.setAttribute("d", context.toString());
```

类似的，RN中使用的path对象和d3中的接口也接近，但序列化方式不同
（d3的生成svg所用的字符串，RN使用数字数组），
而d3中大部分绘图的地方都可以设置context（默认是d3中的path），
这时候可以把context设置为RN中的path，省去转换的消耗。如：

```js
import React from 'react';
import { View, ART } from 'react-native';
import { line } from 'd3-shape';
import { scaleLinear } from 'd3-scale';

const { Path, Surface, Shape } = ART;
const width = 360;
const height = 300;
const data = [5,2,7,6,9,1,8,4,3];
const x = scaleLinear().range([0, width]).domain([0, data.length]);
const y = scaleLinear().range([height, 0]).domain([0, 9]);
const myline = line()
    .x((d, i) => x(i))
    .y(d => y(d));

// use RN's ART Path
const path = Path();
myline.context(path)(data);

const TestArt = () => (
  <View>
    <Surface width={width} height={height}>
      <Shape
        stroke="red"
        d={
          path
        }
      />
    </Surface>
  </View>
);
```

## 使用

实际上，我们一般不会直接操作path，都是通过使用d3-shape来生成类SVG的路径字符串，
传给RN的shape元素，内部会使用RN的path来解析。

## 接口

|接口|d3|art|
|:--|:--|:--|
|moveTo|√|√|
|move||√|
|closePath|√||
|close||√|
|lineTo|√|√|
|line||√|
|quadraticCurveTo|√||
|bezierCurveTo|√||
|curve||√|
|curveTo||√|
|arcTo|√|√|
|arc|√|√|
|counterArc||√|
|counterArcTo||√|
|rect|√||
|toString|√||
|push||√|
|reset||√|

由于art的接口与d3的差别挺大，为了兼容性考虑，
还是用d3自身的path生成SVG的路径字符串传给RN比较合适。
