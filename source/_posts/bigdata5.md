---
title: Hbase
date: 2023-07-06 18:42:04
tags:
categories: 大数据集群搭建
---

## Hbase

**解压文件**

```
tar -zxvf /opt/software/hbase -C /opt/module/
```

------

### 配置环境

```
vi /etc/profile
```

```
export HBASE_HOME=/opt/module/hbase
export PATH=$PATH:$HBASE_HOME/bin
epxort HADOOP_CLASSPATH=${HADOOP_HOME}/lib/*
```

```
source /etc/profile
```

第三条好像不需要进行配置，实测下来不配第三条也能正常运行

------

### 配置文件

#### @hbase-env.sh

```
vi /opt/module/hbase/hbase-env.sh
```

```
export JAVA_HOME=/opt/module/jdk
export HBASE_MANAGES_ZK=false
```

HBASE_MANAGES_ZK含义是不使用`hbase`自带的`zookeeper`

------

#### @hbase-site.xml

```
vi /opt/module/hbase/hbase-site.xml
```

```
<configuration>
	<property>
		<name>hbase.rootdir</name>
		<value>hdfs://master:9000/hbase</value>
	</property>
	<property>
		<name>hbase.cluster.distributed</name>
		<value>true</value>
	</property>
	<property>
		<name>hbase.zookeeper.quorum</name>
		<value>master:2181,slave1:2181,slave2:2181</value>
	</property>
	<property>
		<name>hbase.zookeeper.property.dataDir</name>
		<value>/opt/module/hbase/data</value>
	</property>
	
	//不配置此条命令会导致HMaster进程无法启动
	<property>
		<name>hbase.unsafe.stream.capability.enforce</name>
		<value>false</value>
	</property>
</configuration>
```

hbase.rootdir中的`value`值需要和hadoop中`core-site.xml`中配置的端口号相同，后面的hbase可以自定义；

hbase.zookeeper.property.dataDir记录的是存储日志文件的路径；

**hbase.unsafe.steam.capability.enforce，注意这条命令，经过多次测试，此版本的hbase如果不配置此条指令，会导致hbase启动后，HMaster自动ban掉，进入hbase-shell后输入指令报错！！！**

hbase.zookeeper.quorum中可以只写`master`、`slave1`、`slave2`三个值，但是如果这么写的话，需要再加一条prot来记录zookeeper的端口号。

```
<property>
	<name>hbase.zookeeper.prot</name>
	<value>2181</value>
</property>
```

------

#### @regionservers

```
vi /opt/module/hbase/conf/regionservers
```

```
master
slave1
slave2
```

regionservers中三台节点都需要写入，regionservers用于启动HRegionServer进程，所以，不可以只写分节点。如果只写分节点，会导致无法正常启动HRegionServer进程。

------

### 分发节点与启动集群

#### @分发节点

```
scp -r /opt/module/hbase slave1:/opt/module
scp -r /opt/module/hbase slave2:/opt/module
```

```
scp -r /etc/profile slave1:/etc/profile
scp -r /etc/profile slave2:/etc/profile
```

```
source /etc/profile
```

------

#### @启动集群

```
start-hbase.sh
```

在主节点启动即可。

------

### Hbase Shell

**进入hbase**

```
hbase shell
```

**查看命名空间**

```
list_namespace
```

