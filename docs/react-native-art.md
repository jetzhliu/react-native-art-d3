# react-native 中的art模块的使用

当前RN版本：0.45

由于art模块只提供了最基本的绘图功能，一般建议配合其他模块一起使用，例如
[d3](https://github.com/d3/d3)，
或使用第三方原生扩展的库[react-native-svg](https://github.com/react-native-community/react-native-svg)


## ART 文档

由于react中的art只是实现了部分art标准，所以用前建议先阅读art的文档 [art](http://sebmarkbage.github.io/art/) (react中的art也引入了这个库)。

以下结合标准来介绍在RN中art的使用

## react中提供的art接口

```js
var ReactART = {
  LinearGradient: LinearGradient,
  RadialGradient: RadialGradient,
  Pattern: Pattern,
  Transform: Transform,
  Path: Path,
  Surface: Surface,
  Group: Group,
  ClippingRectangle: ClippingRectangle,
  Shape: Shape,
  Text: Text,
};

module.exports = ReactART;
```

以上接口可以分为以下几部分

### 容器

- Surface
- Group
- ClippingRectangle
- Shape

#### Surface

ART最外层的容器，所有ART元素都必须在Surface容器内。目前已知的属性有：

- w 宽
- h 高
- style 样式，估计与RN中View可接受的style兼容

#### Group

分组，用于组合多个Shape，可以统一操作transform等。
Group必须是Surface的子元素，建议作为Surface的直接子元素。
目前已知的属性有：

- opacity相关属性
- transform相关属性

#### ClippingRectangle

RN中新增的，ART标准中没有。功能与Group基本一致，多了剪裁功能。
目前已知的属性有：

- opacity相关属性
- transform相关属性
- x 剪裁起始x坐标
- y 剪裁起始y坐标
- width 剪裁宽
- height 剪裁高

#### Shape

绘图的基本元素。用于绘制路径以及填充。目前已知的属性有：

- opacity相关属性
- transform相关属性
- stroke 描边颜色
- strokeCap 描边端点处理（butt | round | square，默认round，效果见[stroke-linecap](https://developer.mozilla.org/en/docs/Web/SVG/Attribute/stroke-linecap)）
- strokeDash 描边虚线（数字数组，依次表示实线、空白的长度，效果见[stroke-dasharray](https://developer.mozilla.org/en/docs/Web/SVG/Attribute/stroke-dasharray)）（Android）
- strokeJoin 描边拐角处理（miter | round | bevel，默认round，效果见[stroke-linejoin](https://developer.mozilla.org/en/docs/Web/SVG/Attribute/stroke-linejoin)）
- strokeWidth 描边宽度
- fill 填充颜色（可为单一颜色或渐变）
- d 路径实例，下面Path部分详述

`d`是shape最重要的属性，简单的可以使用Path对象生成，但一般使用第三方库生成，如d3/svg

(注：当前strokeDash在Android下有bug，具体表现为设置的长度没有根据屏幕缩放，如设置视线长度为1，在三倍屏下iOS显示为3物理像素长，而Android只有1物理像素)

#### opacity相关属性

- visible 可见性（设为false相当于opacity=0
- opacity 透明度

#### transform相关属性

- transform Transform实例
- scale 缩放
- scaleX 缩放
- scaleY 缩放
- x 平移
- y 平移
- rotation 旋转
- originX 旋转中心
- originY 旋转中心

### Path

RN 中的Path继承于art库中的Path，接口一致。

常用方法：

- reset 重置
- move 移动（相对当前点）
- moveTo 移动到
- line 连线
- lineTo 连线
- curve 贝塞尔曲线
- curveTo 贝塞尔曲线
- arc 圆弧
- arcTo 圆弧
- counterArc 圆弧（逆时针）
- counterArcTo 圆弧
- close 闭合曲线（链接第一个点）

通过使用move，可以做到一个Path实例包含多个独立的曲线。

### transform

RN中的Transform是直接引用art库中的，主要用于做变换，详见[对应文档](https://github.com/react-native-china/react-native-ART-doc/blob/master/doc.md#transform)

常用方法：

- transform 叠加上某个变形矩阵
- transformTo 重置到某个变形矩阵
- translate 
- move
- scale
- rotate
- moveTo
- rotateTo
- scaleTo
- resizeTo
- point 返回参数表示的坐标点使用该转换后所得的新的坐标
- inversePoint 与point反过来，求原始坐标

### 文本

继承于Shape，拥有Shape所有的属性，并添加字体、字号相关的属性。
可用于生成艺术字。目前已知的属性有：

- Shape的属性
- alignment 对齐方式：left/right/center
- font 参加css中指定font的属性。目前在RN中只有第一个字体会传给native

### 渐变

- LinearGradient(stops, x1, y1, x2, y2)
- RadialGradient(stops, fx, fy, rx, ry, cx, cy)
- Pattern(url, width, height, left, top)

第一个是线性渐变，第二个是径向渐变，第三个是使用图片的渐变。
目前只有iOS支持， * Android尚不支持 * 。

其中`stops`可有以下两种格式

- ['red', 'green', 'blue']
- {'0': 'red', '0.5': 'green', '1': 'blue'}

- x1, y1, x2, y2 是线性渐变的两个端点坐标
- fx, fy 是渐变中心点坐标
- rx, ry 是渐变半径（半径外区域使用第一个颜色填充）
- cx, cy 是渐变的偏移，可以实现不同方向渐变速度不一样

## 与第三方库的配合

### 通过第三方库生成路径对象传给shape的d属性，如d3：

```js
import {
  arc,
} from 'd3-shape';
import {
  ART,
} from 'react-native';
const {
  Path,
  Surface,
  Group,
  Shape,
} = ART;
// 使用RN自带的Path对象
// const d = new Path().moveTo(-80, -80).lineTo(80, 80).lineTo(120, 40);
// 使用d3的art方法生成
const d = arc()
  .outerRadius(100)
  .innerRadius(0)
  .startAngle(0)
  .endAngle(Math.PI)
  ();
class TestArt extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Surface width={500} height={300}>
          <Group x={100} y={100}>
            <Shape
              stroke={'red'}
              d={d}
            />
          </Group>
        </Surface>
      </View>
    );
  }
}
```
