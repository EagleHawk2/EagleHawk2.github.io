---
title: 💯赛前整理
date: 2023-07-19 21:52:15
tags:
categories: Race
---

# 💯赛前整理





## 🥇数据可视化





### 🕐前置配置

---

***

#### 🚩vue.config.js

***

##### 🤐关闭语法检测

```
lintOnSave:false
```

没有关闭语法检测会导致`echarts`引用时出现报错，所以首先需要配置的信息是关闭语法检测

***

##### 😁跨域

```js
devServer:{
	proxy:{
		"/api":{
			target:"接口地址",
            ws:true,
            changeOrigin:true,
            pathRewrite;{
                "^/api":""
            }
		}
	}
}
```

`target`：接口的地址，会给定数据接口。

`ws`：webstocket协议，双向通道，在传输速度上优于http协议。

`changeOrigin`：devServer中，proxy的changeOrigin是false：changeOrigin请求头中host仍然是浏览器发送过来的host；如果设置成true：发送请求头中host会设置成target。在vue-cli3中，默认changeOrigin的值是true,意味着host设置成target，这与cue-cli2不一致，vue-cli2这个默认值是false。

`pathRewrite`：重写策略，在target后会存在其他参数，以/api去添加，但是添加到具体的接口中没有/api这几个字符，所以需要添加一个空字符串在传入参数的时候代替/api。

***

---

#### 🚩全局配置

***

##### 🤔数据请求

post请求和get请求，两个请求方式的请求方法大致相同，在请求时，`method`参数内根据填入的参数不同来进行不同的请求。

post请求有请求体，在安全方面要高于get请求

```
axios:({
	method:"get",
	url:"/api/参数api"
})
```

使用axios进行数据请求，在请求前，可以利用postman工具进行数据接口的调用，检查无误后使用axios进行数据请求。get请求需要配置参数`method`和参数`url`。前者是请求类型，后者是api参数。

```
axios:({
	method:"post",
	url:"/api/参数api",
	data:{
		startTime: yyyy.MM.dd,
		endTime: yyyy.MM.dd
	}
})
```

post请求相对于get请求多了一个body请求体的内容，该内容存放在`data`参数中，也就是需要额外加入的内容。

在postman中，需要根据题目来进行设置，一般body中，传入的参数类型为`json`类型，返回类型也为`json`类型。

***



### 🕑电商

***

***

#### 🚩柱状图

***

##### 😏数据处理

- **🤑消费额最高/最低的省份/地区**

**需求分析：**字段选择消费额、省份/地区。求消费额的最大和最小值的总和，单一累加处理，考虑使用Map对象的get和set方法来拿到消费额总额。累加后需要进行排序，map没有内置的排序方法，将map对象转为数组后进行排序处理，取前几位或后几位使用splice方法进行筛选。

**累加处理：**

```js
//累加处理 利用map对象的set和get方法
var map1 = new Map()
data.forEach((e) =>{
    //判断对象中是否含有省份字段，如果有，则进行累加，如果没有，则传入第一个参数
	if(map1.has(e.proviceName)){
        var tmp = map1.get(e.proviceName) + e.consumptionAmount
        map1.set(e.proviceName , tmp)
    }else{
        map1.set(e.proviceName , e.consumptionAmount)
    }
})
```

**排序筛选处理：**

```js
//对象转为数组
map1 = Array.from(map1)
map1.sort((a,b) =>{
    //降序，升序写法则相反，因为为二维数组，处理数据为第二位，所以返回下标为1
    //sort()方法写法必须是return返回，否则不会进行处理
    return b[1] - a[1]
}).splice(5)
//继续使用splice()方法筛选出前五的数据
```

***

- **😐各个省份/地区消费额的平均数**

**需求分析：**选择字段省份/地区、消费额。需要求平均的消费额，只需要使用map方法提取到对应消费额的每一条数据，然后将所有数据累加再除以数据条数，同时在这一步可以进行保留小数点的操作，即可算出此省份/地区的平均消费额。

**提取数据到数组中：**

```js
var map1 = new Map()
data.forEach((e) =>{
    if(map1.has(e.proviceName)){
        //和累加类似，只是此时传入的map对象中，对应的value值是一个数组。
        var tmp1 = []
        tmp1 = map1.get(e.proviceName)
        tmp1.push(e.consumptionAmount)
        map1.set(e.proviceName , tmp1)
    }else{
        //这里如果第二位不用[]包裹，tmp1无法push任何数据
        //如果这里不用[]包裹，则需要定义一个数组来接收[e.consumptionAount]
        map.set(e.proviceName , [e.consumptionAmount])
    }
})
```

**求平均数处理：**

```js
//定义一个数组来收集求平均值后的数据
var compare = []

map1.forEach((v,k)){
	var sum = 0
	v.forEach((e) =>{
        //求总数
		sum += e
	})
    //求平均数，同时保留两位小数
	var avgNum = (sum / v.length).toFixed(2)
	compare.push({provice:k , avg:avgNum})
}
```

`forEach`：forEach有三个参数，（value，key，item），默认传参为value。其中，value代表的是元素，key代表的是元素索引，即下标，item代表的是整个数组或者对象。当传一个参数是，代表的就是其本身；当传两个参数时，第一个参数代表其元素本身，第二个参数代表下标，当传参对象是一个对象时，需要注意的是，key对应key，value对应value，所以传参中第一个参数为值，第二个参数为键。

**排序筛选处理：**

```js
compare.sort((a,b) =>{
    //根据avg字段进行排序
	return b.avg - a.avg
}).splice(5)
```

***

- **🤨各个省份/地区消费额的中位数**

**需求分析：**选择字段省份/地区、消费额。中位数需要用map提取到所有的消费额数据，然后再对数组进行二次处理，首先先对数组进行排序，然后再取到中位数。

**提取数据到数组中：**

```js
var map1 = new Map()
data.forEach((e) =>{
    if(map1.has(e.proviceName)){  
        //和累加类似，只是此时传入的map对象中，对应的value值是一个数组。
        var tmp1 = []
        tmp1 = map1.get(e.proviceName)
        tmp1.push(e.consumptionAmount)
        map1.set(e.proviceName , tmp1)
    }else{
        //这里如果第二位不用[]包裹，tmp1无法push任何数据
        //如果这里不用[]包裹，则需要定义一个数组来接收[e.consumptionAount]
        map1.set(e.proviceName , [e.consumptionAmount])
    }
})
```

**排序并求中位数：**

```js
//tmp1数组用来拿到前五省份的中位数
var tmp1 = []
map1.forEach((v,k) =>{
	//排序操作
	v.sort((a,b) =>{
		return b - a
	})
    //求中位数
    v.every(() =>{
        //如果是取余为偶数，则中位数为取商和商小一位的数值相加求平均值
        if(v.length % 2 == 0){
            var tmp2 = ((v[v.length / 2] + v[v.length / 2 - 1]) / 2).toFixed(2)
            tmp1.push({province:k , mid:tmp2})
            return false
        }else{
            var tmp2 = v[Math.floor(v.length / 2).toFixed(2)]
            tmp1.push({province:k , mid:tmp2})
            //forEach遍历没有break和continue，可以使用every进行代替，return false返回false，等效于break
            return false
        }
    })
})
```

`break`：forEach遍历方法中不支持break和continue，如果需要使用continue时，可以采用return false或return true代替，如果要使用break时，可以使用try catch或every或some代替。实测return false可以达到break要求。

**排序筛选处理：**

```js
tmp1.sort((a,b) =>{
	return b.mid - a.mid
}).splice(5)
```

***

##### 😎全局样式

- **xAxis**

`type`：category / value

`data`：xdata / ❌

`axisLabel`：rotate:50 / fontSize:18 

- **yAxis**

`type`：category / value

`data`：xdata / ❌

`scale`：true

- **series**

`type`：bar

`data`：ydata

`label`：show:true / position:direction

****

***

#### 🚩折线图

***

##### 😂数据处理

- **😌每年上架商品数量变化**

**数据分析：**字段选择年份、商品名等任意字段。相对于使用计数的方法，其实通过map对象中get方法统计出现次数，然后统计map对象中数组的长度，即可达到统计上架商品数量的变化。

**统计各年份数据：**

```js
var map1 = new Map()
data.forEach((e) =>{
	if(map1.has(e.year)){
        var tmp1 = []
    	tmp1 = map1.get(e.year)
        tmp1.push(e.id)
        map1.set(e.year , tmp1)
    }else
        map1.set(e.year , [e.id])
})
```

**统计长度：**

```js
var xdata = []
var ydata = []
map.forEach((v,k) =>{
	xdata.push(k)
	ydata.push(v.length)
})
```

***

##### 🤪全局样式

- **xAxis**

`type`：category 

`data`：xdata 

`axisLabel`：rotate:50 / fontSize:18 

- **yAxis**

`type`： value

`data`：xdata

`scale`：true

- **series**

`type`：line

`data`：ydata

`label`：show:true / position:direction

***

***

#### 🚩折柱混合图

***

##### 😝数据处理

- **🤯柱状图表示前五省份平均消费额，折线图表示对应地区的平均消费额**

**需求分析：**字段选择省份、地区、消费额。本题难点为如何对应省份和地区，因为对应（主键）字段为省份字段，所以根据省份来进行筛选。map1:{省份 => 省份总额}，map2:{地区 => 地区总额}，map3:{地区 => 地区内省份个数}。用map1对象进行遍历，目标值是拿到每一个省份的名字，再次对元数据进行遍历，如果省份id和元数据中的省份id相等时，那么对省份计数，同时提取出元数据中的地区名称。将地区放入map2和map3中进行对应计算平均消费额，根据省份计数和map1中的省份总销售额进行计算平均消费额。

**map1：**

```js
var map1 = new Map()
tmp1.forEach((e) =>{
	if(map1.has(e.province)){
        tmp1.set(e.province , tmp1.get(e.province) + e.finalTotalAmount)
    }else{
		tmp1.set(e.province , e.finalTotalAmount)
    }
})
```

**map2和map3：**

```js
var map1 = new Map()
tmp1.forEach((e) =>{
	if(map1.has(e.regionName)){
        tmp2.set(e.regionName , tmp2.get(e.regionName) + e.finalTotalAmount)
        tmp3.set(e.regionName , tmp3.get(e.regionName) + 1)
    }else{
		tmp2.set(e.regionName , e.finalTotalAmount)
		tmp3.set(e.regionName , Number(1))
    }
})
```

**对应省份和地区并求平均消费额：**

```js
var res = []
map1.for((v,k) =>{
    //第一步是提取需要的数据
    //提取省份
    var pro = k
    //定义计数器
    var count = 0
    //定义一个地区值用于接收“省份对应地区”
    var reg_compare
    //提取省份对应消费总额，计算需要使用
    var pro_v = v
    //进入元数据开始判断
   	tmp1.forEach((e) =>{
		if(e.provinceName == pro){
            count += 1
            reg_compare = e.regionName
        }
    })
    //计算
    var pro_avg = (pro_v / count).toFixed(2)
    var reg_avg = (map2.get(reg_compare) / map3.get(reg_compare)).toFixed(2)
    res.push({pro:pro , reg:reg_compare , pro_avg:pro_avg , reg_avg:reg_avg})
})
```

**排序筛选处理：**

```js
res.sort((a,b) =>{
    return b.pro_avg - a.pro_avg
}).splice(5)
```

***

##### 🥴全局样式

- **xAxis**

`type`：category 

`data`：xdata 

`axisLabel`：rotate:50 / fontSize:18 

- **yAxis**

`type`： value

`data`：xdata

`scale`：true

- **legend**

`data`：[] / ❌

- **series**

`type`：line ＆ bar

`data`：ydata

`label`：show:true / position:direction

`name`：["图例"]

***

***

#### 🚩饼状图

***

##### 😜数据处理

- **😴饼状图展示各地区消费能力**

**需求分析：**求各地区的消费能力，即以地区为主键字段，统计此地区所有的消费金额/

**统计：**

```js
var map1 = new Map()
tmp1.forEach((e) =>{
	if(map1.has(e.regionName)){
        map1.set(e.regionName , map1.get(e.regionName) + e.finalTotalAmount)
    }else{
        map1.set(e.regionName , e.finalTotalAmount)
    }
})
```

***

- **🥱玫瑰图展示各地区平均消费能力**

**需求分析：**求各地区的消费能力，即以地区为主键字段，统计此地区所有的消费金额的平均额

**求各地区的消费数组：**

```js
var map1 = new Map()
tmp1.forEach((e) =>{
	if(map1.has(e.regionName)){
        var tmp2 = []
        tmp2 = map1.get(e.regionName)
        tmp2.push(e.finalTotalAmount)
        map1.set(e.regionName , tmp2)
    }else{
        map1.set(e.regionName , [e.finalTotalAmount])
    }
})
```

**求平均额：**

```js
var res = []
map1.forEach((v,k) =>{
	var sum = 0
    v.forEach(() =>{
        sum += 1
    })
    var avg = (sum / v.length).toFixed(2)
    res.push({value:k , name:avg})
})
```

***

##### 🤤全局样式

- **series**

`data`：[{value：xx，name：xx}]

`itemStyle`：normal：{label：{show：true ， formatter：" {a} {b} {c} {d}%"}}

`roseType`：radius / area

- **legend**

`data`：[] / ❌

***

***

#### 🚩散点图

***

##### 😤数据处理

- **👹散点图展示每年上架商品数量的变化**
- **👺散点图展示省份平均消费额**

##### 😬全局样式

- **xAxis**

`type`：category / value

`data`：xdata 

`axisLabel`：rotate:50 / fontSize:18 

- **yAxis**

`type`： value / category

`data`：xdata

`scale`：true

- **series**

`type`：scatter

`label`：show：true，position：direction

***



### 🕒工业

***

***

#### 😮‍💨数据处理方法

***

##### 🚩元数据筛选

**.filter()**

```js
var dataOrigin = arr.data.filter((e) =>
	(i.ChangeStartTime.split("T")[0].split("-")[0]) == 2021 &&
    (i.ChangeStartTime.split("T")[0].split("-")[1]) == 10 &&
    (i.ChangeStartTime.split("T")[0].split("-")[2]) == 12
)
```

筛选数据：在元数据中，ChangeStartTime字段，年份为2021年，月份为10月，日期为12日的所有数据。

`filter`：方法用于过滤元素，filter的用法和map相似，但是不同的是，filter会把传入的函数依次用于每一个元素，根据返回值是true或是false来进行判断是保留或是丢弃该元素。

***

##### 🚩数据去重

**.map()**

```js
var deduplication = new Map()
dataOrigin.forEach((e) =>{
    if(!deduplication.has(e.ChangeID)){
        deduplication.set(e.ChangeID , true)
    }
})
```

通过.map()来进行数据的去重，定义一个map对象，遍历元数据，对map对象进行判断，如果不存在ChangID（去重依据），那么就将这一条数据存入map对象中，同时完成数据的记录，完成数据的去重。

***

##### 🚩时间戳转换与计算

> 中国标准时间：Thu Feb 28 2019 17:11:43 GMT+0800
>
> JS默认中国标准时间是 GMT时间.由于我们国家采用的是东八区时间,因此是GMT +0800

> ISO8601标准时间：2019-02-28T09:51:45.540Z
>
> 其中T表示合并,Z表示UTC时间

> 时间戳是指格林威治时间1970年01月01日00时00分00秒(北京时间1970年01月01日08时00分00秒)起至现在的总秒数。
> java的date默认精度是毫秒，也就是说生成的时间戳就是13位的，而像c++或者php生成的时间戳默认就是10位的，因为其精度是秒。

```js
dataOrigin.forEach((e) =>{
	var start = new Date(e.ChangeStartTime).getTime()
    var end = new Date(e.ChangeEndTime).getTime()
    var tim = parseFloat(end - start) / 1000
})
```

new Date()：获取时间，Date对象用于处理日期和时间;

getTime()：方法返回从1970 年 1 月 1 日午夜到指定日期之间的毫秒数；

parseFloat()：函数解析字符串并返回浮点数

> 描述：
>
> ![image-20230728170845753](T:\Web\website\source\_posts\readyRace\image-20230728170845753.png)
>
> 此函数确定指定字符串中的第一个字符是否为数字。如果是，它会解析字符串直到到达数字的末尾，并将数字作为数字而不是字符串返回。
>
> **注意：**只返回字符串中的第一个数字！
>
> **注释：**允许前导和尾随空格。
>
> **注释：**如果第一个字符不能转换为数字，`parseFloat()` 返回 NaN。

***

##### 🚩数据key如何赋值为空

在进行数据处理时，会出现不同的对象对应的值不一样。

比如求某个设备各个状态下的持续时长，可能就会出现某台设备没有某个状态，那么通过map对象set到的方法，就不会显示这一个状态的任何数据。

那么，通过提前存入map(key => value)，就可以避免此问题，如果出现了对应数据，那么则会显示你存入的数据value。

```js
var run = new Map()
var stand = new Map()
var stop = new Map()
tmp1.forEach((e) =>{
	run.set(e.machine , 0)
	stand.set(e.machine , 0)
	stop.set(e.machine , 0)
})
```

通过set提前存入数据，map则会如以下形式，这样即使在后续处理中，某台设备没有此状态的任何数据，也不会忽略此条数据，而是存入了（key => 0）此条数据，避免了后续手动插入数据的麻烦。

map(

{machine1 => 0}，

{machine2 => 0}，

{machine3 => 0})

***

##### 🚩dataset数据集使用情况

目前来说，唯一推荐使用数据集的地方是多条数据、数据成表的格式数据。其他暂时不考虑使用`dataset`

```
dataset:{
	source:[data1,data2,data3]
}
```

> 数据集示例：
>
> ![image-20230728164836822](T:\Web\website\source\_posts\readyRace\image-20230728164836822.png)
>
> data1为数据头，数据头在生成图表后为图例，读取数据方式为从上到下；
>
> data2为数据集，数据集中的头部为图表横坐标；

如果不满足以上条件的图，建议直接绘制，采用data的方式去导入数据，而不采用数据集

***

##### 🚩关于拼接比较

拼接比较的目的是处理部分内容数据，思路是先在处理后数组中将需要保留的字段进行拼接，然后另一个新数组也同时获取这个比较字段，同时增加新字段，新字段就是我们需要处理的部分内容数据，此时相比较两个数组，根据数组的属性（如出现次数，是否存在）来对内容数据进行处理（如增删查改，累加，赋值）。

举个例子⑧🙋🌰：

**每日各车间平均运行时长**

此时，需要抽取的数据为日期信息，车间信息，运行状态信息，以及时长数据。需要处理的是时长，但是处理的方式只能是一对一，所以此时我们将日期信息和车间信息进行拼接来处理时长数据。

```js
let tempArr = [];
          for (let i in state.sumda) {
            let list = state.sumda[i];
            if (!(list.ChangeTime && list.MachineFactory)) {
              // 如果 20GP
              continue;
            } else {
              let compare = {
                type: list.ChangeTime + " " + list.MachineFactory,
              }; //作比较的对象 (因为我需要这种格式，你也可以自定义格式)
              if (tempArr.length == 0) {
                //这段代码必须要写
                let num = Number(list.times);
                let cou = Number(list.count);
                // console.log(num);
                // console.log(cou);
                let obj = {
                  type: list.ChangeTime + " " + list.MachineFactory,
                  total: num,
                  count: cou,
                };
                tempArr.push(obj);
                // console.log(tempArr,"0000000");
              } else {
                let flag = true;
                for (let j in tempArr) {
                  //这个循环只做一个操作，有相同类型的就进行统计
                  if (tempArr[j]["type"] == compare.type) {
                    tempArr[j]["total"] += Number(list.times);
                    tempArr[j]["count"] += Number(list.count);
                    flag = false; //已经统计过的，就不要再重复了
                    break;
                  }
                }
                if (flag) {
                  //如果没有这种类型的，就push进去
                  let num = Number(list.times);
                  let cou = Number(list.count);
                  var obj = {
                    type: list.ChangeTime + " " + list.MachineFactory,
                    total: num,
                    count: cou,
                  };
                  tempArr.push(obj);
                  console.log(tempArr);
                }
              }
            }
          }
          console.log(tempArr);
```

***

##### 🚩删除或添加指定元素

**.splice()**























## 🥈集群搭建