---
title: Flink
date: 2023-07-06 20:27:44
tags:
categories: 大数据集群搭建
---

## Flink配置

**解压文件**

```
tar -zxvf /opt/software/flink -C /opt/module
```

------

### @配置环境

```
vi /etc/profile
```

```
export FLINK_HOME=/opt/module/flink
export PATH=$PATH:$FLINK_HOME/bin
//以下配置是flink on yarn所需要的配置内容
export HADOOP_CONF_DIR=/opt/module/hadoop/etc/hadoop
export HADOOP_CLASSPATH=`/opt/module/hadoop/bin/hadoop classpath`
```

```
source /etc/profile
```

------

### @配置文件

#### #flink-conf.yaml

```
vi /opt/module/flink/conf/flink-conf.yaml
```

```
jobmanager.rpc.address:master
```

------

#### #masters

```
vi /opt/module/flink/conf/masters
```

```
master:8081
```

------

#### #workers

```
vi /opt/module/flink/conf/workers
```

```
slave1
slave2
```

------

### @分发节点和启动集群

#### #分发节点

```
scp -r /opt/module/flume slave1:/opt/module
scp -r /opt/module/flume slave2:/opt/module
```

```
scp -r /etc/profile slave1:/etc
scp -r /etc/profile slave2:/etc
```

```
source /etc/profile
```

------

#### #启动集群

**启动集群**

```
start-cluster.sh
```

**关闭集群**

```
stop-cluster.sh
```

**启动resourcemanager**

```
yarn-daemon.sh start resourcemanager
```

**启动nodemanager**

```
yarn-daemon.sh start nodemanager
```

**执行测试代码**

```
flink run -m yarn-cluster -p 2 -yjm 2G -ytm 2G $FLINK_HOME/examples/batch/WordCount.jar
```

### @部分报错

> ![image-20230907111640351](image-20230907111640351.png)

**出错原因：**

类加载器的相关报错，可能时类加载器的问题

**解决方法：**

可以使用配置”classloader.check-leaked-classloader“禁用此检查

编辑flink-conf.yaml

```
calssloader.leaked-classloader:false
```



