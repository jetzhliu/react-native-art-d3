# 数值格式化

包：d3-format

用于格式化数字（由于计算机使用二进制，以十进制显示会有表示上的误差），如：

```js
for (var i = 0; i < 10; i++) {
  console.log(0.1 * i);
}
```

```plain
0
0.1
0.2
0.30000000000000004
0.4
0.5
0.6000000000000001
0.7000000000000001
0.8
0.9
```

# 本地化设置

d3-format支持设置区域，不同的区域配置放在`local/xx-YY.json`下，如中文：`zh-CN`。

默认配置如下（不需要导入）：

```js
defaultLocale({
  decimal: ".",       // 小数点
  thousands: ",",     // 千分位分隔符
  grouping: [3],      // 多少位分一组，3为1000
  currency: ["$", ""] // 金额符号[前，后]，中文为['￥', '']
});
```

# 方法

- d3.formatLocale         设置本地化格式，返回local对象，带有format和formatPrefix方法
- d3.formatDefaultLocale  设置默认本地化格式，方便后续直接使用`d3.format`等
- d3.format               使用默认区域
- d3.formatPrefix         使用默认区域

# local对象的方法

## format(specifier)

根据`specifier`字符串返回一个格式化函数。如：

```js
var f = d3.format(".1f");
for (var i = 0; i < 10; i++) {
  console.log(f(0.1 * i));
}
```

`specifier`的格式如下：

```plain
[​[fill]align][sign][symbol][0][width][,][.precision][type]
```

以上格式会转化成下方的对象

```flow
class FormatSpecifier {
  fill: string;
  align: string;
  sign: string;
  symbol: string;
  zero: bool;
  width: number;
  comma: bool;
  precision: string;
  type: string;
}
```

### fill

填充字符。当width比数字的字符串表示要长时，使用fill填充剩余空位，默认为空格` `。

### align

对齐方式。与fill配合指示填充的位置。只能使用下方四种符号。默认为`>`。

- > - 文本在右，填充在左
- < - 文本在左，填充在右
- ^ - 文本居中
- = - 类似`>`，但符号和标记（正负号，货币符号）位于最左

### sign

符号（正负号）。只能使用下方四中符号。默认为`-`。

- `-` - 负数前加负号，其他不加
- `+` - 非负数也要加`+`号
- `(` - 负数用括号括起，没有`-`号
- ` ` 空格 - 非负数前加空格，负数前加`-`号

### symbol

标识，只能是下方两种之一，默认为空

- `$` - 使用货币符号
- `#` - 增加2/8/16进制前缀，分别为`0b`，`0o`，`0x`

### zero

是fill为`0`，align为`=`的快捷方式

### width

最小宽度

### comma

是否使用分组，如添加千分位（具体根据local的grouping设置

### precision

精度。type不一样时，含义不同。有两种含义

- 小数点后面的数字个数（type为`f`和`%`）
- 有效数字（type为`e`，`g`，`r`，`s`和`p`）

当精度没设置时，若type也没设置，则为6，否则为12；
当精度有设置是，若type为`gprs`，精度范围为1-21，否则为0-20；
对于整数，精度会被忽略。

### type

类型，默认为空``。不同类型的格式化方式不同。如下所示：

- `` - 和`g`类似，但会省去尾部的`0`
- `c`: 直接转成文本（同toString）
- `d`: 整数，采用四舍五入
- `f`: 小数，四舍五入
- `%`: 以百分比显示，如`0.1`显示为`10%`
- `e`: 指数，四舍五入
- `g`: 小数或指数（根据有效数字决定，有效数字足够表示整数部分的话，使用小数表示）
- `p`: 百分比表示，采用四舍五入，与`%`不同在于对于精度的处理不同，见上方
- `r`: 小数，四舍五入，与`f`不同在于对于精度的处理不同，见上方
- `s`: 小数，会自动采用`SI`前缀，如`0.0001`显示成`1.00000m`，具体参考`formatPrefix`
- `b`: 先取正，再显示为2进制
- `o`: 先取正，再显示为8进制
- `x`: 先取正，再显示为16进制，`a-f`
- `X`: 先取正，再显示为16进制，`A-F`


## formatPrefix(specifier, value)

类似format中type为`s`，但可根据value指定使用的`SI`前缀，并且精度代表小数点后的个数（类似`f`）。
具体的值对应如下：

- `y` - yocto, 10⁻²⁴
- `z` - zepto, 10⁻²¹
- `a` - atto, 10⁻¹⁸
- `f` - femto, 10⁻¹⁵
- `p` - pico, 10⁻¹²
- `n` - nano, 10⁻⁹
- `µ` - micro, 10⁻⁶
- `m` - milli, 10⁻³
- `​` (none) - 10⁰
- `k` - kilo, 10³
- `M` - mega, 10⁶
- `G` - giga, 10⁹
- `T` - tera, 10¹²
- `P` - peta, 10¹⁵
- `E` - exa, 10¹⁸
- `Z` - zetta, 10²¹
- `Y` - yotta, 10²⁴

## formatSpecifier

根据入参生成FormatSpecifier对象，参见上方`specifier`的格式

## 其他

下面三个方法都是根据变化步长来计算建议的精度的，常用于构造`specifier`

- precisionFixed(step) 用于类型为`f%`
- precisionPrefix(step, value) 用于`d3.formatPrefix`方法
- precisionRound(step, max) 用于类型为`egprs`

示例：

```js
var p = d3.precisionFixed(0.5),
    f = d3.format("." + p + "f");
f(1);   // "1.0"
f(1.5); // "1.5"
f(2);   // "2.0"
```
