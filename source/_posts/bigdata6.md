---
title: Spark
date: 2023-07-06 18:42:09
tags:
categories: 大数据集群搭建
---

## Spark配置

**解压文件**

```
tar -zxvf /opt/software/spark -C /opt/module/
```

------

### @配置环境

```
vi /etc/profile
```

```
export SPARK_HOME=/opt/module/spark
export PATH=$PATH:$SPARK_HOME/bin
```

```
source /etc/profile
```

------

### @配置文件

#### #spark-config.sh(sbin)

```
vi /opt/module/spark/sbin/spark-config.sh
```

```
export JAVA_HOME=/opt/module/jdk
```

注意这里是进入`sbin`目录下的`spark-config.sh`中进行配置`jdk`

------

#### #spark-env.sh

```
cp spark-env.sh.template spark-env.sh
vi /opt/module/spark/conf/spark-env.sh
```

```
export JAVA_HOME=/opt/module/jdk
export SPARK_HOME=/opt/module/spark
export SPARK_LOCAL_DIR=${SPARK_HOME}/tmp
#on yarn配置
export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop
export YARN_CONF_DIR=${HADOOP_HOME}/etc/hadoop
```

SPARK_LOCAL_DIR配置的路径为spark的tmp目录；

HADOOP_CONF_DIR配置的路径为hadoop的conf（hadoop下的etc/hadoop）

YARN_CONF_DIR配置的路径为hadoop的conf（hadoop下的etc/hadoop）

------

#### #workers

```
vi /opt/module/spark/workers
```

```
slave1
slave2
```

同样的，这个文件对应老版本的`slaves`文件，新版问为`workers`，配置内容相同

------

#### #yarn-site.xml

在hadoop/etc/hadoop下

spark on yarn配置时，需要在hadoop下的yarn文件中添加两行配置，在hadoop中有讲解

```
<configuration>
	<property>
		<name>yarn.nodemanager.pmem-check-enabled</name>
		<value>false</value>
	</property>
	<property>
		<name>yarn.nodemanager.vmem-check-enabled</name>
		<value>false</value>
	</property>
</configuration>
```

------

### @分发节点与启动集群

#### #分发节点

```
scp -r /opt/module/spark slave1:/opt/module
scp -r /opt/module/spark slave2:/opt/module
```

```
scp -r /etc/profile slave1:/etc/
scp -r /etc/profile slave2:/etc/
```

```
source /etc/profile
```

------

#### #启动集群

```
start-all.sh
```

------

### @Spark Shell

#### #进入shell

```
spark shell
```

#### #运行架包

```
spark-submit --master yarn --class org.apache.spark.examples.SparkPi  $SPARK_HOME/examples/jars/spark-examples_2.12-3.1.1.jar
```

