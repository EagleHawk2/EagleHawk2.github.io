---
title: Hadoop
date: 2023-06-29 19:18:30
tags:
categories: 大数据集群搭建
---



**写在最前，本文中所使用的配置是可以达到使用hadoop集群搭建的最基本配置，如果有其他需求，按照自己的需求去增加或删除配置文件即可，本文只阐述最基本用例，后期优化就靠自己啦~加油噢💪**



## Hadoop完全分布式安装

**解压到指定文件夹**

```
tar -zxvf /opt/software/hadoop.tar.gz -C /opt/module/
```

每一个文件的配置无外乎就两个配置内容，一个是环境变量的配置，一个是文件内的配置。而要使用的第一步，自然就是安装。我们采用的安装方法是压缩包安装，所以我们安装的第一步，是解压文件

------

### **@配置环境变量**

```
vi /etc/profile
```

```
export HADOOP_HOME=/opt/module/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

以上是hadoop2.7版本中环境变量需要配置的内容。

在这里再提一个Linux shell语法，可以快速查询路径：

**查询当前所在路径名**

```
pwd
```

但是在hadoop的3.1.3的版本中，需要加入以下内容，否则会报错。幸运的是，报错内容也会提示你如何去修改环境变量。（这是第一种配置方式，文章结尾的补充说明中，我会讲述第二种配置方式🤣当然我还是觉得这种配置起来更方便啦）

```
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
```

hadoop3.1.3版本需要加入以上内容。

**全局环境变量生效**

```
source /etc/profile
```

每次修改完环境变量需要对环境变量进行生效命令。

关于环境变量的生效，再补充一些内容。

如果当出现全局变量配置出现问题导致基础命令失效的情况——

有一个最高位的命令可以使用：

**全局变量修改**

```
/bin/vi /etc/profile
```

**全局变量生效**

```
./etc/profile
```

### **@配置文件**

**配置文件模板**

```
<configuration>
	<property>
		<name></name>
		<value></value>
	</property>
</configuration>
```

没啥用，存一个，方便复制粘贴。

------

#### #core-site.xml

```
vi /opt/module/hadoop/etc/hadoop/core-site.xml
```

```
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://master:9000</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/opt/module/hadoop/data</value>
	</property>
</configuration>
```

NameNode内部通信的端口在hadoop3.1.3版本中有三个常用端口号：8020，9000，9820，我常用的端口号是9000。需要注意的是，Clickhouse的默认端口号也是9000，所以当需要配置两个的时候，有一个需要更改端口号，否则会出现占用的情况。

hadoop.tmp.dir存储的是hadoop文件系统依赖，它有默认位置在根目录的/tmp下，但是存储于默认位置下的tmp文件是临时文件，在机器进行开关时会重置丢失文件，所以我们需要手动为它配置一个路径。我写的data文件夹是安装包不自带的，data文件在执行NameNode的初始化时会自动生成，也可以自己为它创建。

**新建data文件夹**

```
mkdir data
```

------

#### #hdfs-site.xml

```
vi /opt/module/hadoop/etc/hadoop/hdfs-site.xml
```

```
<configuration>
	<property>
		<name>dfs.namenode.http-address</name>
		<value>master:9870</value>
	</property>
	<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>slave1:9868</value>
	</property>
</configuration>
```

dfs.namenode.http-address是dfs namenode web ui使用的监听地址和基本端口，我这里设置的基本端口号为9870。

dfs.namenode.secondary,http-address同理是SecondaryNameNode使用的监听地址和基本端口，我这里设置的基本端口号为9868。

------

#### #yarn-site.xml

```
vi /opt/module/hadoop/etc/hadoop/yarn-site.xml
```

```
<configuration>
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>slave2</value>
	</property>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
</configuration>
```

yarn.reosurcemanager.hostname，如名字所示，就是用于指定resourcemanager的主机名。

yarn.nodemanager.aux-services属性用于指定在进行mapreduce作业时，yarn使用mapreduce_shuffle混洗技术。shuffle是MapReduce中重要的组成部分，工作原理这里就不进行赘述了。

具体的工作原理我放到其他文章里进行讲述，这里我们先讲配置。

------

#### #mapred-site.xml

```
vi /opt/module/hadoop/etc/hadoop/mapred-site.xml
```

```
<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
</configuration>
```

mapreduce.framework.name属性用于执行MapReduce作业的运行时框架。属性值可以是local，classic，yarn。什么情况下使用什么属性值，我们在其他文章里再进行讲述。

在hadoop2.7版本中，mapred-site.xml是一个模板文件，文件名为：mapred-site.xml.template，如果要进行配置，就复制模板文件，在复制的新文件中进行配置。

**复制模板文件**

```
cp mapred-site.xml.template mapred-site.xml
```

意为，复制模板文件，命名为：mapred-site.xml

#### #hadoop-env.sh

```
vi /opt/module/hadoop/etc/hadoop/hadoop-env.sh
```

```
export JAVA_HOME=/opt/module/jdk
```

关于hadoo-env.sh，在hadoop2.7的版本中，我会配置三个.sh文件（hadoop-env.sh、yarn-env.sh、mapred-env..sh）的jdk路径，但是具体用处不太了解（等我查一查再说），在hadoop3.1.3的版本中，我尝试过不配置jdk路径，发现会报错，于是配置了hadoop-env.sh这个文件的jdk路径，解决问题。所以结论是需要配置这个文件，这个文件的具体含义等我查了再进行补充。

#### #wokers

```
vi /opt/module/hadoop/etc/hadoop/workers
```

```
master
slave1
slave2
```

wokers文件的原身是旧版本中的slaves，在hadoop2.7版本中，此文件名为slaves，在hadoop3.1.3版本中，此文件名为wokers，虽然更改的名字，但是文件的配置内容不发生改变。

### @启动集群

#### #拷贝文件

```
scp -r /opt/module/hadoop/ slave1:/opt/module
scp -r /opt/module/hadoop/ slave2:/opt/module
```

```
scp -r /etc/profile slave1:/etc/
scp -r /etc/profile slave2:/etc/
```

将hadoop、环境变量拷贝到两外两个节点，同时注意另外两个节点要进行环境变量的生效。

#### #初始化NameNode

```
cd /opt/module/hadoop/bin
```

```
hdfs namenode -format
hadoop namenode -format
```

两种命令都可以进行NameNode的初始化，不过需要注意的是，根据版本不同，有一些命令可能会出现被淘汰的情况，不过目前来说，并未禁用，仍然可以继续使用，所以无伤大雅🤣

> 配一张格式化成功的图片
>
> <img src="S:\BigData\EagleHawk.github.io\image\image-20230630141110386.png" alt="image-20230630141110386" style="zoom:50%;" />
>
> 出现标红字体即代表初始化成功

需要说明的是，如果更改了根目录下的一些文件，则需要重新初始化NameNode，以保证NamNode的配置，如pid文件不会出现问题，否则，集群可能无法启动，无法使用。

#### #启动集群

```
cd /opt/module/hadoop/sbin
```

```
start-dfs.sh
start-yarn.sh
start-all.sh
```

需要说明的是，启动不同集群需要在配置的那台机器上进行启动，比如我的ResourceManager配置在了slave2节点上，那么我就要进入到slave2中hadoop的sbin文件夹下启动start-yarn.sh，才能正常启动ResourceManager以及NodeManager。

其中三条命令，要么选择start-dfs.sh加start-yarn.sh，要么选择start-all.sh，二选其一即可。

> 启动完成后，可以通过jps来查询各节点进程，正确的进程状态如下表
>
> | 节点名 |                   拥有进程                    |
> | :----: | :-------------------------------------------: |
> | master |     NameNode、DataNode、Jps、NodeManager      |
> | slave1 | SecondaryNameNode、DataNode、Jps、NodeManager |
> | slave2 |  ResourceManager、DataNode、Jps、NodeManager  |
>
> 进程所在节点根据不同的集群规划会在不同的地方，但是以上进程必须拥有，如果没有对应进程，则证明启动失败，需要检查日志文件、报错信息进行配置文件的修改。



## Hadoop伪分布式安装

**有关Hadoop的伪分布式配置，就简单的提一嘴，Hadoop的伪分布式是在只有一台机器时，模拟的集群配置，所以和完全分布式配置不同的地方在于，因为只有一台机器，所以所有内容必须搭建在同一台机器，即所有节点端口，都配置在master节点上（看你仅存的节点名叫什么hhh🤣）即可。**





## Hadoop on Yarn(HA)

**写在最前：关于HadoopHA的内容，个人理解不深，目前只能达到能做题目的水平，对配置项内的参数也并不是十分理解。此外，还需要注意的是，关于HadoopHA的后续安装，目前实测只成功了kafka、hive，失败报错flume的数据采集部分（仍未解决）😭😭。**

Hadoop 的HA有两个前置环境需要安装，第一个是JAVA环境，第二个是Zookeeper环境。

JAVA环境在第一章中我们已经进行过了配置，这里就不再赘述。接下来我们来配置i所需要的第二种环境——

### Zookeeper配置

**解压文件**

```
tar -zxvf /opt/software/zookeeper.tar.gz -C /opt/module/
```

解压压缩包到指定文件夹中。

------

#### @配置环境变量

```
vi /etc/profile
```

```
export ZOOKEEPER_HOME=/opt/module/apache-zookeeper
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

```
source /etc/profile
```

#### @配置文件

##### #myid

```
cd /opt/module/apache-zookeeper
```

```
mkdir data
```

```
echo 1 > data/myid
```

对于zookeeper，首先是每个节点的myid值，这个myid值再进行文件拷贝后，需要在不同的节点写上不同的值，值必须和zoo.cfg文件中server.x中x的值相同，注意对应节点。我的配置下，master节点为1，slave1节点为2，slave2节点为3。

##### #zoo.cfg

```
cd /opt/module/apche-zookeeper/conf
```

```
cp zoo_sample.cfg zoo.cfg
```

```
vi /opt/module/apache-zookeeper/zoo.cfg
```

```
dataDir=/opt/module/apache-zookeeper/data
server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
```

首先进入到zookeeper的conf文件夹下，conf文件夹下有一个zoo_sample.cfg文件，我们需要对这个进行修改，但是同样的，这个文件也只是一个模板文件，所以在进行修改配置之前，我们要先拷贝此文件。

zoo.cfg文件的修改内容为两处——

**dataDir**，这条属性存储的是zookeeper各个节点的myid值，路径设置为zookeeper根目录下的data文件夹，也就是指向myid值即可

**server.x=ip:2888:3888**，这条属性就是对应myid的值，x即你在当前节点下myid所需要配置的值，ip可以是IP名也可以是IP地址，具体是什么，可以具体情况都试试看（🤣），因为曾经出现过IP名报错，但是IP地址可以使用的情况，但是hhh网上的都说不可以用IP地址，唔，具体原因还没有研究过，等后面再研究了看。

#### @启动集群

```
cd /opt/module/apache-zookeeper/bin
```

```
zkServer.sh start
```

```
zkServer.sh status
```

zookeeper的启动是有说法的，这个原理关系到zookeeper的选举机制。这里就先不进行赘述了。主要分为两个进程——Mode：follower和Mode：Leader。

同时还有一个需要注意的地方，zookeeper的启动需要三台同时启动，这个可以借助Xshell工具或者是自主编写脚本，或者另一种方式是单节点的一台一台启动。其中启动实践应该是控制在30000ms以内（还没找到具体的时间要求，但是很久以前好像在哪里看到过）。根据zookeeper的选举机制，第一台和第三台启动的节点为follow节点，第二台启动的节点为leader节点。

可以通过

**查询状态**

```
zkServer.sh status
```

> 启动完成后jps查询
>
> | 节点名称 |    进程状态    |
> | :------: | :------------: |
> |  master  | Mode：follower |
> |  slave1  |  Mode：leader  |
> |  slave2  | Mode：follower |
>
> zookeeper正常启动后进程状态如上表所示，注意：jps查询不会有结果

### HadoopHA部署

**解压文件**

```
tar -zxvf /opt/software/hadoop-3.1.3 -C /opt/module/
```

***

#### @配置环境变量

这里配置的版本是hadoop-3.1.3，所以环境变量中多出来的七项，在hadoop-2.7版本中不需要配置

```
vi /etc/profile
```

```
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/bin
//以下是hadoop-3.1.3版本中需要额外配置的，这些配置内容还可以配置在启动文件或者是hadoop-env.sh中
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
//以下是hadoop-3.1.3版本中HA所需要的新配置内容
export HDFS_ZKFC_USER=root
export HDFS_JOURNALNODE_USER=root
```

以上内容在启动时，如果没有配置或是配置有误，会`error`报错提示，但是，这里推荐配置好，不要到时候再进行修改，可能会出现解决不了报错问题。

#### @配置文件

##### #hadoop-env.sh

```
vi /opt/module/hadoop-3.1.3/etc/hadoop/hadoop-env.sh
```

```
export JAVA_HOME=/opt/module/jdk
```

##### #core-site.xml

```
vi /opt/module/hadoop-3.1.3/etc/hadoop/
```

```
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://mycluster</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/opt/module/hadoop-3.1.3/dfs/tmp</value>
	</property>
	<property>
		<name>ha.zookeeper.quorum</name>
		<value>master:2181,slave1:2181,slave2:2181</value>
    </property>

	<!-- 👇❌ -->

	<property>
		<name>io.file.buffer.size</name>
		<value>131072</value>
    </property>
	<property>
		<name>hadoop.proxyuser.root.hosts</name>
		<value>*</value>
	</property>
	<property>
		<name>hadoop.proxyuser.root.groups</name>
		<value>*</value>
	</property>
	<property>
		<name>hadoop.proxyuser.root.users</name>
		<value>*</value>
	</property>
</configuration>
```

`fs.defaultFS`：Hadoop FS 客户端使用的默认路径

`hadoop.tmp.dir`：Hadoop临时文件路径

`ha.zookeeper.quorum`：配置zk集群

`dfs.journalnode.edits.dir`：JournalNode守护进程将存储其本地状态的路径

`io.file.buffer.size`：SequenceFiles中使用的读/写缓冲区的大小

`hadoop.proxyuser.root.hosts`：配置允许访问的主机

`hadoop.proxyuser.root.groups`：配置允许访问的用户组

`hadoop.proxyuser.root.users`：配置运行访问的用户

***

##### #hdfs-site.xml

```
vi /opt/module/hadoop-3.1.3/etc/hadoop/hdfs-site.xml
```

```
<configuration>
	<property>
		<name>dfs.nameservices</name>
		<value>mycluster</value>
    </property>
    <property>
		<name>dfs.ha.namenodes.mycluster</name>
		<value>nn1,nn2</value>
    </property>
    <property>
		<name>dfs.namenode.rpc-address.mycluster.nn1</name>
		<value>master:8020</value>
    </property>
    <property>
		<name>dfs.namenode.rpc-address.mycluster.nn2</name>
		<value>slave1:8020</value>
    </property>
    <property>
		<name>dfs.namenode.http-address.mycluster.nn1</name>
		<value>master:9870</value>
    </property>
    <property>
		<name>dfs.namenode.http-address.mycluster.nn2</name>
		<value>slave1:9870</value>
    </property>
    <property>
		<name>dfs.namenode.shared.edits.dir</name>
		<value>qjournal://master:8485;slave1:8485;slave2:8485/mycluster</value>
    </property>
	<property>
		<name>dfs.journalnode.edits.dir</name>
		<value>/opt/module/hadoop-3.1.3/data/jn</value>
	</property>
	<property>
		<name>dfs.ha.automatic-failover.enabled</name>
		<value>true</value>
    </property>
    <property>
		<name>dfs.client.failover.proxy.provider.mycluter</name>
		<value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
    </property>
    <property>
		<name>dfs.ha.fencing.methods</name>
		<value>sshfence
			shell(/bin/bash)
		</value>
    </property>
    <property>
		<name>dfs.ha.fencing.ssh.private-key-files</name>
		<value>/root/.ssh/id_rsa</value>
    </property>

	<!-- 👇❌ -->

    <property>
		<name>dfs.ha.fencing.ssh.connection-timeout</name>
		<value>30000</value>
    </property>
    <property>
		<name>dfs.datanode.data.dir</name>
		<value>/opt/module/hadoop-3.1.3/dfs/data</value>
    </property>
    <property>
		<name>dfs.blocksize</name>
		<value>268435456</value>
    </property>
    <property>
		<name>dfs.namenode.handler.count</name>
		<value>100</value>
    </property>
</configuration>
```

`dfs.nameservices`：nameservices逻辑名称

`dfs.ha.namenodes.mycluster`：nameservices服务中每个NameNode的唯一标识符

`dfs.ha.namenode.rpc-address.mycluster.nnX`：nnX NameNode监听的完全限定的RPC地址

`dfs.ha.namenode.http-address.mycluster.nnX`：nnX NameNode监听的完全限定的HTTP地址

`dfs.namenode.shared.edits.dir`：标识NameNode 将在其中写入/读取编辑的 JN 组的URL

`dfs.client.failover.proxy.provider.mycluster`：HDFS客户端用来联系Active NameNode的Java类

`dfs.ha.fencing.methods`：一个脚本或JAVA类的列表，将在故障转移期间用于隔离Active NameNode

`dfs.ha.fencing.ssh.private-key-files`：SSH 到 Active NameNode 并终止进程

`dfs.ha.fencing.ssh.connect-timeout`：SSH连接超时时长(毫秒)

`dfs.ha.automatic-failover.enabled`：开启自动故障转移

`dfs.replication`：Hadoop的副本数量，默认为3

`dfs.namenode.name.dir`：在本地文件系统所在的NameNode的存储空间和持续化处理日志

`dfs.datanode.data.dir`：在本地文件系统所在的DataNode的存储空间和持续化处理日志

`dfs.blocksize`：用于大型文件系统的 HDFS 块大小为 256MB

`dfs.namenode.handler.count`：NameNode 服务器线程来处理来自大量DataNode 的 RPC

***

##### #yarn-site.xml

```
vi /opt/module/hadoop-3.1.3/etc/hadoop/yarn-site.xml
```

```
<configuration>
	<property>
  		<name>yarn.nodemanager.aux-services</name>
  		<value>mapreduce_shuffle</value>
	</property>
	<property>
  		<name>yarn.resourcemanager.ha.enabled</name>
  		<value>true</value>
	</property>
	<property>
  		<name>yarn.resourcemanager.cluster-id</name>
  		<value>RMcluster</value>
	</property>
	<property>
  		<name>yarn.resourcemanager.ha.rm-ids</name>
  		<value>rm1,rm2</value>
	</property>
	<property>
  		<name>yarn.resourcemanager.hostname.rm1</name>
  		<value>master</value>
	</property>
	<property>
  		<name>yarn.resourcemanager.hostname.rm2</name>
  		<value>slave1</value>
	</property>
	<property>
  		<name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
	</property>
	<property>
  		<name>yarn.nodemanager.vmem-check-enabled</name>
  		<value>false</value>
	</property>
	<property>
  		<name>hadoop.zk.address</name>
  		<value>master:2181,slave1:2181,slave2:2181</value>
	</property>

	<!-- 👇❌ -->

	<property>
  		<name>yarn.scheduler.minimum-allocation-mb</name>
  		<value>1024</value>
	</property>
	<property>
  		<name>yarn.scheduler.maximum-allocation-mb</name>
  		<value>4096</value>
	</property>
	<property>
  		<name>yarn.nodemanager.resource.cpu-vcores</name>
  		<value>5</value>
	</property>
	<property>
  		<name>yarn.resourcemanager.ha.enabled</name>
  		<value>true</value>
	</property>
	<property>
  		<name>yarn.resourcemanager.webapp.address.rm1</name>
  		<value>master:8088</value>
	</property>
	<property>
  		<name>yarn.resourcemanager.webapp.address.rm2</name>
  		<value>slave1:8088</value>
	</property>
</configuration>
```

`yarn.scheduler.minimum-allocation-mb`：在资源管理器中分配给每个容器请求的最小内存限制。以 MB 为单位

`yarn.scheduler.maximum-allocation-mb`：在资源管理器中分配给每个容器请求的最大内存限制。以 MB 为单位

`yarn.nodemanager.resource.cpu-vcores`：可以为容器分配的 vcore 数量，这不用于限制 YARN 容器使用的物理内核数。默认为8

`yarn.resourcemanager.ha.enabled`：启用 RM HA

`yarn.resourcemanager.cluster-id`：标识集群。选举人使用它来确保 RM 不会接管另一个集群的活动

`yarn.resourcemanager.ha.rm-ids`：RM 的逻辑 ID 列表

`yarn.resourcemanager.hostname.rmX`：指定 RM逻辑id,rmX对应的主机名

`yarn.resourcemanager.webapp.address.rmX`：指定 RM逻辑id,rmX对应的web地址

`hadoop.zk.address`：指定hadoop集群

***

##### #mapred-site.xml

```
vi /opt/module/hadoop-3.1.3/etc/hadoop/mapred-site.xml
```

```
<configuration>
	<property>
  		<name>mapreduce.framework.name</name>
  		<value>yarn</value>
	</property>

	<!-- 👇❌ -->

	<property>
  		<name>yarn.app.mapreduce.am.env</name>
  		<value>HADOOP_MAPRED_HOME=/opt/module/hadoop-3.1.3</value>
	</property>
	<property>
  		<name>mapreduce.map.env</name>
  		<value>HADOOP_MAPRED_HOME=/opt/module/hadoop-3.1.3</value>
	</property>
	<property>
  		<name>mapreduce.reduce.env</name>
  		<value>HADOOP_MAPRED_HOME=/opt/module/hadoop-3.1.3</value>
	</property>
	<property>
  		<name>mapreduce.map.memory.mb</name>
  		<value>2048</value>
	</property>
	<property>
  		<name>mapreduce.map.java.opts</name>
  		<value>-Xmx1536M</value>
	</property>
	<property>
  		<name>mapreduce.reduce.memory.mb</name>
  		<value>4096</value>
	</property>
	<property>
  		<name>mapreduce.map.java.opts</name>
  		<value>-Xmx2560M</value>
	</property>
	<property>
  		<name>mapreduce.jobhistory.address</name>
  		<value>master:10020</value>
	</property>
	<property>
  		<name>mapreduce.jobhistory.webapp.address</name>
  		<value>master:19888</value>
	</property>
	<property>
  		<name>mapreduce.jobhistory.intermediate-done-dir</name>
  		<value>/mr-history/tmp</value>
	</property>
	<property>
  		<name>mapreduce.jobhistory.done-dir</name>
  		<value>/mr-history/done</value>
	</property>
</configuration>
```

`mapreduce.framework.name`：执行框架设置为 Hadoop YARN

`mapreduce.map.memory.mb`：mapreduce.map.memory.mb

`mapreduce.map.java.opts`：map任务子jvm的堆大小

`mapreduce.reduce.memory.mb`：reduce任务的资源限制

`mapreduce.map.java.opts`：reduce任务子jvm的堆大小

`mapreduce.jobhistory.address`：MapReduce JobHistory 服务器主机：端口

`mapreduce.jobhistory.webapp.address`：MapReduce JobHistory Server Web UI主机：端口

`mapreduce.jobhistory.intermediate-done-dir`：MapReduce 作业写入历史文件的目录

`mapreduce.jobhistory.done-dir`：历史文件由 MR JobHistory Server 管理的目录

***

##### #workers

```
vi /opt/module/hadoop-3.1.3/etc/hadoop/workers
```

```
master
slave1
slave2
```

***

#### @启动集群

- 分发节点
- 启动JournalNode（3台节点）

```
hdfs --daemon start journalnode
```

- 格式化namenode

```
hdfs namenode -format
```

- 格式化zkfc

```
hdfs zkfc -formatZK
```

- 启动集群

```
start-all.sh
```

- 备用NameNode复制元数据目录（2台分节点）

```
hdfs namenode -bootstrapStandby
```

```
hdfs --daemon start namenode
```

- 查看所有NameNode状态

```
hdfs haadmin -getAllServiceState
```

- 运行Pi程序测试

```
yarn jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar pi 10 10
```

- 重启master namenode

```
hdfs --daemon start namenode
```

```
hdfs haadmin -getServiceState nn1
```


