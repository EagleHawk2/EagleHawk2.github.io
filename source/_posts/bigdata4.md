---
title: Hive
date: 2023-07-06 18:41:57
tags:
categories: 大数据集群搭建
---
## Hive配置

### MySQL配置

mysql的配置比较复杂和麻烦，建议单独配置好了再进行下一步（虚拟机配置mysql成功后记得多拷贝几份！！！）

### Hive配置

**解压文件**

```
tar -zxvf /opt/software/apache-hive -C /opt/module
```

---

#### @配置环境

```
vi /etc/profile
```

```
export HIVE_HOME=/opt/module/apache-hive
export PATH=$PATH:$HIVE_HOME/bin
```

```
source /etc/profile
```

#### @配置文件

##### #hive-site.xml

```
vi /opt/module/apache-hive/conf/hive-site.xml
```

```
<configuration>
	<property>
		<name>javax.jdo.option.ConnectionURL</name>
		<value>jdbc:mysql://localhost/hive?createDatabaseIfNotExist=true</value>
	</property>
	<property>
		<name>javax.jdo.option.ConnectionDriverName</name>
		<value>com.mysql.jdbc.Driver</value>
	</property>
	<property>
		<name>javax.jdo.option.ConnectionUserName</name>
		<value>root</value>
	</property>
	<property>
		<name>javax.jdo.option.ConnectionPassword</name>
		<value>123456</value>
	</property>
</configuration>
```

hive-site.xml配置文件可以由hive-default.xml.tamplate模板文件复制而来，也可以直接使用 `vi`来进行书写创建。基础的hive配置只需要完成上述四条的配置即可。

ConnectionURL，mysql数据库的访问路径，没有路径则总动创建；

ConnectionDriverName，连接mysql数据库的驱动；

ConnectionUserName，连接mysql数据库的用户名；

ConnectionPassword，连接mysql数据库的秘密。

---

##### #hive-env.sh

```
vi /opt/module/apache-hive/conf/hive-env.sh
```

```
export JAVA_HOME=${JAVA_HOME}
export HIVE_HOM=${HIVE_HOME}
export HADOOP_HOME=${HADOOP_HOME}
export HIVE_CONF_DIR=${HIVE_HOME}/conf
```

这里需要配置 `jdk`、`hadoop`、`hive`、`hive/conf`四条路径信息

---

#### @架包

##### #mysql-connect.jar.gz

```
cp /opt/software/mysql-connection.jar.gz /opt/module/apache-hive/lib
```

mysql的连接架包，在老版本中，给出的mysql架包是一个压缩包，其中有两个架包需要移动；

而在新版本中，只需要移动这一个架包即可。

---

##### #guava.jar

```
cp /opt/module/hadoop/share/hadoop/common/lib/guava2.7.jar /opt/module/apache-hive/lib
```

guava.jar是hadoop3.1.3新版本中的问题，是apache项目中对应的版本无法和hadoop版本对应导致的问题，所以当前版本报错，需要把hadoop下的2.x版本的架包移动到对应项目的lib文件夹下，并且删除以前的旧版本，即可完成。

**删除guava1.x.jar**

```
rm -rf /opt/module/apache-hive/lib/guava.1.x.jar
```

删除老版本架包再运行。

---

#### @初始化与启动

##### #初始化

```
schematool -dbType mysql -initSchema
```

这个版本的hive初始化时，会出现大片空白，经过咨询，是正常情况。

---

##### #启动

```
hive --service metaservice &
```
