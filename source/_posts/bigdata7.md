---
title: Flume
date: 2023-07-06 20:27:38
tags:
categories: 大数据集群搭建
---

## Flume配置

**解压文件**

```
tar -zxvf /opt/software/apache-flume -C /opt/module/
```

------

### @配置环境

```
vi /etc/profile
```

```
export FLUME_HOME=/opt/module/apache-flume
export PATH=$PATH:$FLUME_HOME/bin
```

------

### @配置文件

#### #flume-env.sh

```
vi /opt/module/flume/conf/flume-env.sh
```

```
export JAVA_HOME=/opt/module/jdk
```

flume-env.sh是temlate模板文件，需要自己进行复制

------

#### #log4j.properties

```
vi /opt/module/flume/conf/log4j.properties
```

```
flume.log.dir=/opt/module/flume/logs
flume.log.file=flume.log
```

------

### @架包

#### #guava.jar

```
cp /opt/module/hadoop/share/hadoop/common/lib/guava2.7.jar /opt/module/apache-flume/lib
```

和hive一样，guava.jar是hadoop3.1.3新版本中的问题，是apache项目中对应的版本无法和hadoop版本对应导致的问题，所以当前版本报错，需要把hadoop下的2.x版本的架包移动到对应项目的lib文件夹下，并且删除以前的旧版本，即可完成。

**删除guava1.x.jar**

```
rm -rf /opt/module/apache-flume/lib/guava.1.x.jar
```

删除老版本架包再运行。

------

### @数据采集

flume的数据采集，可以在flume根目录下新建一个文件夹来保存数据采集的文件，创建flume采集文件需要用`.conf`做后缀名

#### #创建

```
mkdir -p /opt/module/apache-flume/job/a1.conf
```

```
vi /opt/module/apache-flume/job/a1.conf
```

------

#### #文件配置

##### 方法一：exec类型

```shell
#使用s1、k1、c1代表source、sink、channel
a1.sources = s1
a1.sinks = k1
a1.channels = c1

#descirbe/configuration the source
a1.source.s1.type = exec
#采集数据的位置，题目要求一般是namenode或是datanode，如果使用或，则两个都可
a1.source.s1.command = tail -F /opt/module/hadoop/logs/hadoop-root-namenode-master.log

#describe the sink
a1.sinks.k1.type = hdfs
#采集后存储的位置
a1.sinks.k1.hdfs.path = hdfs://master:9000/flume1

#use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

#bind the source and sink to the channel
a1.sources.s1.channels = c1
a1.sinks.k1.channel = c1
```

##### 方法二：TAILDIR类型

```
q1.sources = s1
q1.sinks = k1
q1.channels = c1

q1.sources.s1.type = TAILDIR
q1.sources.s1.filegroups = f1
q1.sources.s1.filegroups.f1 = /opt/module/hadoop/logs/.*log

q1.sinks.k1.type = hdfs
q1.sinks.k1.hdfs.path = hdfs://master:9000/flume2

q1.channels.c1.type = memory
q1.channels.c1.capacity = 1000
q1.channels.c1.transactionCapacity = 100

q1.sources.s1.channels = c1
q1.sinks.k1.channel = c1
```

根据测试，`sinks.path`后面的地址要和`core-site.xml`文件中的地址一样，后面的文件名自定义即可

根据测试，根目录下不创建表也可以进行数据采集。

------

#### #启动采集

**首先启动hdfs和yarn**

**启动采集文件**

```
bin/flume-ng agent --conf conf/ --name a1 --conf-file job/netcat-flume-logger.conf -Dflume.root.logger=INFO,console
```

-Dflume.root.logger=INFO,console表示输出采集过程；

--name后面为agent名字，即配置文件中最前面的`a1`；

--conf后面为flume/conf的路径地址

--conf-file后面为项目文件地址

------

#### #查看文件

**查看采集的数据**

```
hdfs dfs -ls -R /flume1
```

**创建表文件**

```
hadoop fs -mkdir -p /flume
```

根据测试，根目录下不创建表也可以进行数据采集。写在这里以防外一需要。