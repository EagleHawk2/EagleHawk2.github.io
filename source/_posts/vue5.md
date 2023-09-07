---
title: 🐔通用配置项
date: 2023-07-11 14:35:10
tags:
categories: Echarts
---
### 常用配置项

[echarts配置项文档]: https://echarts.apache.org/zh/option.html#title

#### title:{}

在 ECharts 2.x 中单个 ECharts 实例最多只能拥有一个标题组件。但是在 ECharts 3 中可以存在任意多个标题组件，这在需要标题进行排版，或者单个实例中的多个图表都需要标题时会比较有用。

------

##### show=

是否显示标题组件。

```
show = ture
```

------

##### text=

主标题文本，支持使用 `\n` 换行。

```
text = "主标题文本"
```

------

##### textStyle:{}

tex文本属性。

- color = "颜色"

- fontStyle= "字体风格"
  - normal
  - italic
  - oblique

------

##### subtext=

副标题文本，支持使用 `\n` 换行。

```
subtext = "副标题"
```

------

##### subtextStyle:{}

tex文本属性。

- color = "颜色"

- fontStyle= "字体风格"
  - normal
  - italic
  - oblique

------

##### left=

title 组件离容器左侧的距离。

`left` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比，也可以是 `'left'`, `'center'`, `'right'`。

如果 `left` 的值为 `'left'`, `'center'`, `'right'`，组件会根据相应的位置自动对齐。

```
left = "20%"
```

------

##### top=

title 组件离容器上侧的距离。

`top` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比，也可以是 `'top'`, `'middle'`, `'bottom'`。

如果 `top` 的值为 `'top'`, `'middle'`, `'bottom'`，组件会根据相应的位置自动对齐。

```
top = "50%"
```

------

##### right=

title 组件离容器右侧的距离。

`right` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比。

默认自适应。

```
right = "20%"
```

------

##### bottom=

title 组件离容器下侧的距离。

bottom 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比。

默认自适应。

```
bottom = "50%"
```

***



#### legend:{}

图例组件。

图例组件展现了不同系列的标记(symbol)，颜色和名字。可以通过点击图例控制哪些系列不显示。

ECharts 3 中单个 echarts 实例中可以存在多个图例组件，会方便多个图例的布局。

当图例数量过多时，可以使用 滚动图例（垂直）或 滚动图例（水平）。

***

##### type=

图例的类型。可选值：

- `'plain'`：普通图例。缺省就是普通图例。
- `'scroll'`：可滚动翻页的图例。当图例数量较多时可以使用。

```
type = "plain"
```

***

##### left=

图例组件离容器左侧的距离。

`left` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比，也可以是 `'left'`, `'center'`, `'right'`。

如果 `left` 的值为 `'left'`, `'center'`, `'right'`，组件会根据相应的位置自动对齐。

```
left = "auto"
```

***

##### top=

title 组件离容器上侧的距离。

`top` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比，也可以是 `'top'`, `'middle'`, `'bottom'`。

如果 `top` 的值为 `'top'`, `'middle'`, `'bottom'`，组件会根据相应的位置自动对齐。

```
top = "50%"
```

------

##### right=

title 组件离容器右侧的距离。

`right` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比。

默认自适应。

```
right = "20%"
```

------

##### bottom=

title 组件离容器下侧的距离。

bottom 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比。

默认自适应。

```
bottom = "50%"
```

***

##### orient=

图例列表的布局朝向。

- `'horizontal'`
- `'vertical'`

```
orient = 'horizontal'
```

***

##### textStyle:{}

图例的公用文本样式。

**color = "颜色"**

**fontStyle = "字体风格"**

- nromal
- italic
- oblique

**fontSize = "字体大小"**

***



#### xAxis&yAxis:{}

直角坐标系 grid 中的 x 轴，一般情况下单个 grid 组件最多只能放上下两个 x 轴，多于两个 x 轴需要通过配置 offset属性防止同个位置多个 x 轴的重叠。

***

##### show=

是否显示 x 轴。

```
show = "true"
```

***

##### position=

x 轴的位置。

可选：

- `'top'`
- `'bottom'`

```
position = "top"
```

***

##### type=

坐标轴类型。

可选：

- `'value'` 数值轴，适用于连续数据。
- `'category'` 类目轴，适用于离散的类目数据。为该类型时类目数据可自动从 series.data 或 dataset.source 中取，或者可通过 xAxis.data 设置类目数据。
- `'time'` 时间轴，适用于连续的时序数据，与数值轴相比时间轴带有时间的格式化，在刻度计算上也有所不同，例如会根据跨度的范围来决定使用月，星期，日还是小时范围的刻度。
- `'log'` 对数轴。适用于对数数据。

```
type = "category"
```

***

##### name=

坐标轴名称。

```
name = "坐标轴名称"
```

***

##### nameTextStyle=

坐标轴名称的文字样式。

**color = "颜色"**

**fontStyle = "字体风格"**

- nromal
- italic
- oblique

**fontSize = "字体大小"**

***

##### scale=

只在数值轴中（type: 'value'）有效。

是否是脱离 0 值比例。设置成 true 后坐标刻度不会强制包含零刻度。在双数值轴的散点图中比较有用。

在设置 min 和 max 之后该配置项无效。

```
scale = "true"
```

***

##### interval=

强制设置坐标轴分割间隔。

因为 splitNumber 是预估的值，实际根据策略计算出来的刻度可能无法达到想要的效果，这时候可以使用 interval 配合 min、max 强制设定刻度划分，一般不建议使用。

无法在类目轴中使用。在时间轴（type: 'time'）中需要传时间戳，在对数轴（type: 'log'）中需要传指数值。

```
interval = 50
```

***

##### axisLine:{}

坐标轴轴线相关设置。

**show = "true"**

**symbol = 'none'**

轴线两边的箭头。可以是字符串，表示两端使用同样的箭头；或者长度为 2 的字符串数组，分别表示两端的箭头。默认不显示箭头，即 `'none'`。两端都显示箭头可以设置为 `'arrow'`，只在末端显示箭头可以设置为 `['none', 'arrow']`。

***

##### axisTick:{}

坐标轴刻度相关设置。

**show = "true"**

坐标轴刻度相关设置。

**alignWithLabel = ture**

类目轴中在 `boundaryGap` 为 `true` 的时候有效，可以保证刻度线和标签对齐

**interval = "auto"**

坐标轴刻度的显示间隔，在类目轴中有效。默认同 `axisLabel.interval` 一样。

默认会采用标签不重叠的策略间隔显示标签。

可以设置成 0 强制显示所有标签。

如果设置为 `1`，表示『隔一个标签显示一个标签』，如果值为 `2`，表示隔两个标签显示一个标签，以此类推。

***

##### axisLable:{}

坐标轴刻度标签的相关设置。

**show = "true"**

**interval = "auto"**

**inside = "ture"**

**formatter:{}**

刻度标签的内容格式器，支持字符串模板和回调函数两种形式。

|     分类     |  模板  |                         取值（英文）                         |                      取值（中文）                      |
| :----------: | :----: | :----------------------------------------------------------: | :----------------------------------------------------: |
|     Year     | {yyyy} |                    e.g., 2020, 2021, ...                     |                  例：2020, 2021, ...                   |
|              |  {yy}  |                            00-99                             |                         00-99                          |
|   Quarter    |  {Q}   |                          1, 2, 3, 4                          |                       1, 2, 3, 4                       |
|    Month     | {MMMM} |                 e.g., January, February, ...                 |                     一月、二月、……                     |
|              | {MMM}  |                     e.g., Jan, Feb, ...                      |                      1月、2月、……                      |
|              |  {MM}  |                            01-12                             |                         01-12                          |
|              |  {M}   |                             1-12                             |                          1-12                          |
| Day of Month |  {dd}  |                            01-31                             |                         01-31                          |
|              |  {d}   |                             1-31                             |                          1-31                          |
| Day of Week  | {eeee} | Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday | 星期日、星期一、星期二、星期三、星期四、星期五、星期六 |
|              |  {ee}  |              Sun, Mon, Tues, Wed, Thu, Fri, Sat              |               日、一、二、三、四、五、六               |
|              |  {e}   |                             1-54                             |                          1-54                          |
|     Hour     |  {HH}  |                            00-23                             |                         00-23                          |
|              |  {H}   |                             0-23                             |                          0-23                          |
|              |  {hh}  |                            01-12                             |                         01-12                          |
|              |  {h}   |                             1-12                             |                          1-12                          |
|    Minute    |  {mm}  |                            00-59                             |                         00-59                          |
|              |  {m}   |                             0-59                             |                          0-59                          |
|    Second    |  {ss}  |                            00-59                             |                         00-59                          |
|              |  {s}   |                             0-59                             |                          0-59                          |
| Millisecond  | {SSS}  |                           000-999                            |                        000-999                         |
|              |  {S}   |                            0-999                             |                         0-999                          |

***

##### data:[{}]

类目数据，在类目轴（type: 'category'）中有效。

如果没有设置 type，但是设置了 axis.data，则认为 type 是 'category'。

如果设置了 type 是 'category'，但没有设置 axis.data，则 axis.data 的内容会自动从 series.data 中获取，这会比较方便。不过注意，axis.data 指明的是 'category' 轴的取值范围。如果不指定而是从 series.data 中获取，那么只能获取到 series.data 中出现的值。比如说，假如 series.data 为空时，就什么也获取不到。

***



#### radar:{}

雷达图坐标系组件，只适用于雷达图。该组件等同 ECharts 2 中的 polar 组件。因为 3 中的 polar 被重构为标准的极坐标组件，为避免混淆，雷达图使用 radar 组件作为其坐标系。

雷达图坐标系与极坐标系不同的是它的每一个轴（indicator 指示器）都是一个单独的维度，可以通过 name、axisLine、axisTick、axisLabel、splitLine、 splitArea 几个配置项配置指示器坐标轴线的样式。

***

##### center=[]

中心（圆心）坐标，数组的第一项是横坐标，第二项是纵坐标。

支持设置成百分比，设置成百分比时第一项是相对于容器宽度，第二项是相对于容器高度。

```
center:["50%" , "50%"]
```

***

##### radius=

半径。可以为如下类型：

- `number`：直接指定外半径值。
- `string`：例如，`'20%'`，表示外半径为可视区尺寸（容器高宽中较小一项）的 20% 长度。

- `Array.<number|string>`：数组的第一项是内半径，第二项是外半径。每一项遵从上述 `number` `string` 的描述

```
radius = "50%"
```

***

##### startAngle =

坐标系起始角度，也就是第一个指示器轴的角度。

```
startAngle = 90
```

***

##### axisName:{}

雷达图每个指示器名称的配置项。

**show = "true"**

**formatter:{}**

**color = "颜色"**

**fontStyle = "文字风格"**

- `'normal'`
- `'italic'`
- `'oblique'`

**fontSize = "字体大小"**

***

#### singleAxis:{}

单轴。可以被应用到散点图中展现一维数据。

***

##### left=

title 组件离容器左侧的距离。

`left` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比，也可以是 `'left'`, `'center'`, `'right'`。

如果 `left` 的值为 `'left'`, `'center'`, `'right'`，组件会根据相应的位置自动对齐。

```
left = "20%"
```

------

##### top=

title 组件离容器上侧的距离。

`top` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比，也可以是 `'top'`, `'middle'`, `'bottom'`。

如果 `top` 的值为 `'top'`, `'middle'`, `'bottom'`，组件会根据相应的位置自动对齐。

```
top = "50%"
```

------

##### right=

title 组件离容器右侧的距离。

`right` 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比。

默认自适应。

```
right = "20%"
```

------

##### bottom=

title 组件离容器下侧的距离。

bottom 的值可以是像 `20` 这样的具体像素值，可以是像 `'20%'` 这样相对于容器高宽的百分比。

默认自适应。

```
bottom = "50%"
```

***

##### orient=

轴的朝向，默认水平朝向，可以设置成 `'vertical'` 垂直朝向。

```
orient = "horizontal"
```

***

##### width=

single组件的宽度。默认自适应。

```
width = "auto"
```

***

##### height=

single组件的高度。默认自适应。

```
height = "auto"
```

***

##### type=

坐标轴类型。

可选：

- `'value'` 数值轴，适用于连续数据。
- `'category'` 类目轴，适用于离散的类目数据。为该类型时类目数据可自动从 series.data或 dataset.source 中取，或者可通过singleAxis.data设置类目数据。
- `'time'` 时间轴，适用于连续的时序数据，与数值轴相比时间轴带有时间的格式化，在刻度计算上也有所不同，例如会根据跨度的范围来决定使用月，星期，日还是小时范围的刻度。
- `'log'` 对数轴。适用于对数数据。

```
type = "category"
```

***

##### name=

坐标轴名称。

***

##### fontStyle=

坐标轴名称文字字体的风格。

可选：

- `'normal'`
- `'italic'`
- `'oblique'`

***

##### nameGap=

坐标轴名称与轴线之间的距离。

```
nameGap = 15
```

***

##### boundaryGap=

坐标轴两边留白策略，类目轴和非类目轴的设置和表现不一样。

类目轴中 `boundaryGap` 可以配置为 `true` 和 `false`。默认为 `true`，这时候刻度只是作为分隔线，标签和数据点都会在两个刻度之间的带(band)中间。

非类目轴，包括时间，数值，对数轴，`boundaryGap`是一个两个值的数组，分别表示数据最小值和最大值的延伸范围，可以直接设置数值或者相对的百分比，在设置 min和 max 后无效。

```
boundaryGap:["20%" , "20%"]
```

***

##### scale=

只在数值轴中（type: 'value'）有效。

是否是脱离 0 值比例。设置成 `true `后坐标刻度不会强制包含零刻度。在双数值轴的散点图中比较有用。

在设置 min 和 max 之后该配置项无效。

***

##### axisLine=

坐标轴轴线相关设置。

**show = "true"**

**symbol = "none"**

**symbolSize = [10,15]**

***



#### dataset:{}

ECharts 4 开始支持了 `数据集`（`dataset`）组件用于单独的数据集声明，从而数据可以单独管理，被多个组件复用，并且可以自由指定数据到视觉的映射。这在不少场景下能带来使用上的方便。

***

##### source=

原始数据。一般来说，原始数据表达的是二维表。可以用如下这些格式表达二维

表：

二维数组，其中第一行/列可以给出维度名，也可以不给出，直接就是数据：

```
[
    ['product', '2015', '2016', '2017'],
    ['Matcha Latte', 43.3, 85.8, 93.7],
    ['Milk Tea', 83.1, 73.4, 55.1],
    ['Cheese Cocoa', 86.4, 65.2, 82.5],
    ['Walnut Brownie', 72.4, 53.9, 39.1]
]
```

按行的 key-value 形式（对象数组），其中键（key）表明了维度名：

```
[
    {product: 'Matcha Latte', count: 823, score: 95.8},
    {product: 'Milk Tea', count: 235, score: 81.4},
    {product: 'Cheese Cocoa', count: 1042, score: 91.2},
    {product: 'Walnut Brownie', count: 988, score: 76.9}
]
```

按列的 key-value 形式，每一项表示二维表的 “一列”：

```
{
    'product': ['Matcha Latte', 'Milk Tea', 'Cheese Cocoa', 'Walnut Brownie'],
    'count': [823, 235, 1042, 988],
    'score': [95.8, 81.4, 91.2, 76.9]
}
```

