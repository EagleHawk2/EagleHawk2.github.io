---
title: 折线图
date: 2023-07-01 09:43:11
tags: 
categories: Echarts
---
## 🧵Echarts折线图

### 基础折线图

**样式:**

```
option = {
  xAxis: {
    type: 'category',
    data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      data: [150, 230, 224, 218, 135, 147, 260],
      type: 'line'
    }
  ]
};
```

> 图表示例
> ![line1](line1.png)
>
> **解释**
>
> |  属性  |      type属性      | data属性 |
> | :----: | :----------------: | :------: |
> | xAxis | category（类目型） | x轴数据 |
> | yAxis |  value（数值型）  |          |
> | series |   line（折线图）   | y轴数据 |
>
> 坐标轴的默认属性是数值型，而 `xAxis`指定了类目型的 `data`，所以 `Echarts`也可以识别出这是类目轴的坐标轴。在实际应用中，当 `type`值是 `value`时，可以省略不写。

---

#### 笛卡尔坐标系中的折线图

**样式:**

```
option = {
  xAxis: {},
  yAxis: {},
  series: [
    {
      data: [
        [20, 120],
        [50, 200],
        [40, 50]
      ],
      type: 'line'
    }
  ]
};
```

> 图表示例
>
> ![line2](line2.png)
>
> 如果我们希望折线图在横坐标和纵坐标上都是连续的，即在笛卡尔坐标系中，应该如何实现呢？答案也很简单，只要把 `series`的 `data` 每个数据用一个包含两个元素的数组表示就行了。

---

#### 基础折线图样式

折线图中的样式在 `series`中使用 `lineStyle`进行设置

**样式:**

|    属性    |         值         |                 用法                 |
| :--------: | :-----------------: | :-----------------------------------: |
| `color` |    rgb/rgba/#fff    |               折线颜色               |
|   width   |          2          |              折线的宽度              |
|  `type`  | solid/dashed/dotted |           实现/虚线/短虚线           |
| dashOffset |      [Number]      |           实现虚线的偏移量           |
|    cap    |  butt/round/square  | 线段末端结束方式：方形/圆形/方形+矩形 |

---

### 区域面积图

相比于基础折线图，区域面积图是堆叠的折线图。

**样式:**

```
option = {
  xAxis: {
    data: ['A', 'B', 'C', 'D', 'E']
  },
  yAxis: {},
  series: [
    {
      data: [10, 22, 28, 23, 19],
      type: 'line',
      areaStyle: {}
    },
    {
      data: [25, 14, 23, 35, 10],
      type: 'line',
      areaStyle: {
        color: '#ff0',
        opacity: 0.5
      }
    }
  ]
};
```

> 图表示例
>
> ![line3](line3.png)
>
> 通过 `areaStyle`设置折线图的填充区域样式，将其设为为 `{}` 表示使用默认样式，即，使用系列的颜色以半透明的方式填充区域。如果想指定特定的样式，可以通过设置 `areaStyle` 下的配置项覆盖，如第二个系列将填充区域的颜色设为不透明度为 0.5 的黄色。

---

#### 区域面积图样式

区域面积图的样式根据 `areaStyle`进行配置

|    属性    |           值           |                    用法                    |
| :--------: | :---------------------: | :-----------------------------------------: |
| `color` |      rgb/rgba/#fff      |                区域填充颜色                |
| `origin` | auto/start/end/[number] | 图形区域的起始位置：默认/底部/顶部/指定数值 |

---

### 平滑曲线图

**样式:**

```
option = {
  xAxis: {
    data: ['A', 'B', 'C', 'D', 'E']
  },
  yAxis: {},
  series: [
    {
      data: [10, 22, 28, 23, 19],
      type: 'line',
      smooth: true
    }
  ]
};
```

> 图表示例
>
> ![line4](line4.png)
>
> 平滑曲线图也是折线图的一种变形，这种更柔和的样式也是一种不错的视觉选择。使用时，只需要将折线图系列的 `smooth` 属性设置为 `true` 即可。
