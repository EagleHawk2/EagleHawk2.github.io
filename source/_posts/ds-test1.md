---
title: 电商题目示例
date: 2023-07-10 20:23:18
tags:
categories: 可视化示例
---
## 可视化示例——柱状图

### 柱状图（Bar）

#### 消费额最高的省份

**考点：数据提取、数据排序**

**数据接口：Post请求**

**绘图方式：柱状图**

- 用柱状图展示2020年消费额最高的5个省份。

- 本题考查的是消费额最高的省份，那么就从元数据中提取到各省份的消费总额，消费总额中让各消费额累加，所以采用`map`对象来进行操作，通过`set`和`get`方法，我们能够拿到需要的字段，用`map => Array`表示。
  - 定义一个变量
  - 变量直接`get`接收所需要的省份ID和省份总消费额
  - 将变量`set`到`map`对象中


- 标准柱状图echarts参数`xAxis`/`yAxis`/`series`/`legend`
  - x轴数据填写省份ID，类型为类目型
  - y轴类型为数值型
  - 系列中填写y轴数据对应省份的消费总额，类型为柱状图，显示图表数据使用`label{show position}`参数，图例使用`name`进行设置，使用`legend`进行显示 
  - 图例参数中如果不传入数据，则使用系列中`name`数据默认显示

```vue
<template>
  <div>
    <h1>消费额最高的省份</h1>
    <p>用柱状图展示2020年消费额最高的5个省份</p>
    <div id="t1" style="width: 1600px; height: 600px"></div>
  </div>
</template>

<script>
//数据可视化需要导包，axios用于接口请求数据，echarts用于绘制图表，onMounted是钩子生命周期
import axios from "axios";
import { onMounted } from "vue";
import * as echarts from "../assets/echarts"
export default {
  setup() {
    //也就是说所有的数据处理都在这一个生命周期里完成
    onMounted(() => {
      axios({
        //请求方式为post请求
        method: "post",
        url: "/api//dataVisualization/selectOrderInfo",
        //请求方式post的body请求体
        data: {
          startTime: "2020-01-01 00:00:00",
          endTime: "2020-12-30 00:00:00",
        },
      })
      .then((ar) =>{
        var t1 = ar.data.data
        console.log("元数据",t1);
        //定义一个map对象来对数据进行处理
        var tmp1 = new Map()
        //遍历元数据，开始提取数据
        t1.forEach((e) =>{
            //如果tmp1对象中有provinceName这个数据
            if(tmp1.has(e.provinceName)){
                //get到provinceName值，同时对finalTotalAmount值进行累加，赋值给tmp2
                var tmp2 = tmp1.get(e.provinceName) + e.finalTotalAmount
                //tmp1进行set，set是填入数据，set方法会更新这个map对象，tmp1存储provinceName值，以及销售额的累加值
                tmp1.set(e.provinceName , tmp2)
            }else{
                //如果tmp1对象中没有provinceName值，就需要存入初值
                tmp1.set(e.provinceName , e.finalTotalAmount)
            }
        })
        console.log(tmp1);
        //map对象可以通过Array.from()方法直接转成数组
        tmp1 = Array.from(tmp1)
        //使用.sort()方法对数组进行排序，排序内部(a-b)为升序，(b-a)为降序，结果必须return，否则不排序
        var Sort = tmp1.sort((a,b) =>{
            return b[1] - a[1]
        })
        //.splice()方法可以显示数据量，此处只显示前五条数据
        Sort.splice(5)
        console.log(Sort);
        var xData = []
        var yData = []
        //遍历数组，x轴填入数组的provinceName值，y轴填入数组的finalTotalAmount值
        Sort.forEach((e) =>{
            xData.push(e[0])
            yData.push(e[1])
        })
        //使用echarts的格式
        var chartDom = document.getElementById("t1")
        var myCharts = echarts.init(chartDom)
        //option中填充echarts所需要的样式和数据
        var option = {
            xAxis:{
                data:xData,
                type:"category"
            },
            yAxis:{
                type:"value"
            },
            legend:{
            },
            series:[
                {
                    name:"消费额",
                    type:"bar",
                    data:yData,
                    label:{
                        show:true,
                        position:"top"
                    }
                }
            ]
        }
        //使用echarts的格式
        option && myCharts.setOption(option , true)
      })
      .catch((err) =>{
        console.log(err);
      })
    })
  },
};
</script>
```

> 



------



### 饼状图（Pie）

#### 消费总额占比

**考点：数据累加、绘图数据处理**

**数据接口：Post请求**

**绘图方式：饼状图**

- 用饼状图展示2020年各地区的消费总额占比
- 在数据处理方面，需要的是各地区的消费总额，所以利用`map`对象来提取地区名和地区消费总额，对消费总额进行累加。
- 

```vue
<template>
  <div>
    <h1>各地区消费能力</h1>
    <p>用饼状图展示2020年各地区的消费总额占比</p>
    <div id="t2" style="width: 1600px; height: 600px"></div>
  </div>
</template>
  
  
  <script>
import axios from "axios";
import { onMounted } from "vue";
import * as echarts from "../assets/echarts"
export default {
  setup() {
    onMounted(() => {
      axios({
        method: "post",
        url: "/api//dataVisualization/selectOrderInfo",
        data: {
          startTime: "2020-01-01 00:00:00",
          endTime: "2020-12-30 00:00:00",
        },
      })
        .then((ar) => {
          var t1 = ar.data.data;
          console.log("元数据", t1);
          var tmp1 = new Map();
          t1.forEach((e) => {
            if (tmp1.has(e.regionName)) {
              var tmp2 = tmp1.get(e.regionName) + e.finalTotalAmount;
              tmp1.set(e.regionName, tmp2);
            }else{
              tmp1.set(e.regionName , e.finalTotalAmount)
            }
          })
          // console.log(tmp1);
          tmp1 = Array.from(tmp1)
          console.log(tmp1);
          var tmp2 = []
          //饼状图数据形式：数组对象[{name,value},{...},{...}]
          tmp1.forEach((e) =>{
            var obj = {}
            obj.name = e[0]
            obj.value = e[1]
            tmp2.push(obj)
          })
          console.log(tmp2);
          var chartDom = document.getElementById("t2");
          var myCharts = echarts.init(chartDom);
          var option = {
            legend:{
            },
            series:{
              data:tmp2,
              type:"pie",
              itemStyle:{
                normal:{
                  label:{
                    show:true,
                    formatter: '{b}  ({c} {d}%)'
                  }
                }
              }
            }
          };
          option && myCharts.setOption(option, true);
        })
        .catch((err) => {
          console.log(err);
        });
    });
  },
};
</script>
```

