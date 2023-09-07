---
title: Kafka
date: 2023-06-29 19:18:40
tags:
categories: 大数据集群搭建
---

## Kafka安装配置

### Zookeeper安装配置

在配置Kafka之前，需要安装zookeeper

**解压文件**

```
tar -zxvf /opt/software/zookeeper.tar.gz -C /opt/module/
```

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

------

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

------

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





### Kafka安装配置

当zookeeper配置完成后，即可开始配置kafka

#### @配置环境变量

```
vi /etc/profile
```

```
export KAFKA_HOME=/opt/module/kafka
export PATH=$PATH:$KAFKA_HOME/bin
```

------

#### @配置文件

在配置文件时，有两个思路，一个 是启用kafka自带的zookeeper，另一个是启动zookeeper后单独启动kafka。在这里我使用的是第二种方法，第一种方法的配置内容在文末再进行说明。

##### #server.properties

```
broker.id=1
listeners=PLAINTEXT://192.168.100.3.9092
log.dirs=/opt/module/kafka/kafka-logs
zookeeper.connect=master:2181,slave1:2181,slave2:2181
```

broker.id为自定义的数字，可以自己设定，但是需要注意的是，kafka集群中每台节点的id值不可以相等。

listeners为监听地址，配置主节点的ip地址即可。

log.dirs为kafka的日志文件路径，路径自定义即可，日志文件夹会自动创建。

zookeeper.connect配置为三台节点的zookeeper端口和ip名称即可。

------

如果不适用自带的zookeeper，上述命令即可完成kafka搭建。

下面的内容是使用自带的zookeeper需要配置的。

##### #data/zk

这里的配置方法和zookeeper中的相同，也是在根目录下新建文件夹，然后在文件夹中写入myid，注意各节点的myid值需要对应server后面的值。同时，后面dataDir对应的路径名也是myid对应的路径名。

##### #zookeeper.properties

```
vi /opt/module/kafka/config/zookeeper.properties
```

```
tickTime=2000
initLimit=10
syncLimit=5
clientPort=2181
dataDir=/opt/moduele/kafka/data/zk
server.1=192.168.100.3:2888:3888
server.2=192.168.100.4:2888:3888
server.3=192.168.100.2:2888:3888
```

此文件的配置内容和zookeeper中zoo.cfg内容相同，唯一需要注意的是zoo.cfg的模板文件会添加好前几条内容作为默认配置，但是zookeeper.properties中需要自己手动添加。

#### @启动集群

**拷贝集群**

```
scp -r /opt/module/kafka slave1:/opt/module/
scp -r /opt/module/kafka slave1:/opt/module/
scp -r /etc/profile slave1:/etc/
scp -r /etc/profile slave2:etc/
source /etc/profile
```

**更改配置**

记得在从节点上更改broker.id和listeners监听端口的ip地址（虽然我之前好行忘记改ip地址也正常启动了，但是不知道不配置会不会有什么影响，还是配上吧哈哈。）

------

**启动Zookeeper（内置）**

```
./bin/zookeeper-server-start.sh config/zookeeper.properties &
```

**启动Kafka**

```
./bin/kafka-server.start.sh config/server.properties &
```

**停止zk/kafka**

```
./kafka-server-stop.sh
./zookeeper-server.stop.sh
```

再开其实要先开启zk再开启kafka，同理关闭的时候也应该先关闭kafka再关闭zk（应该用不着）

------

#### @JPS

启动完成后，需要进行查询状态进程来确认是否成功启动了zk和kafka

> ```
> jps
> ```
>
> | 节点名称 |       进程状态        |
> | :------: | :-------------------: |
> |  master  | QuorumPeerMain、kafka |
> |  slave1  | QuorumPeerMain、kafka |
> |  slave2  | QuorumPeerMain、kafka |
>
> QuorumPeerMain进程为zookeeper成功启动后的进程
>
> kafka为kafka成功启动后的进程

**需要强调的是，如果创建kafka-topic中replication-facor（副本数）大于1的话，需要启动分节点的kafka进程，如果不启动分节点的kafka，则无法创建，会报错broker值小于副本数。**

------





### Kafka-shell

简单的创建topic

#### kafka-topic

创建方法有两个

**需要强调的是，如果创建kafka-topic中replication-facor（副本数）大于1的话，需要启动分节点的kafka进程，如果不启动分节点的kafka，则无法创建，会报错broker值小于副本数。**

##### bootstrap

```
./bin/kafka-topics.sh  --bootstrap-server localhost:9092(,loclalhost1:9092,localhost2:9092) --list
```

- --list	查询集群中是否有topic

- --bootstrap-server localhost:9092	连接服务器，**localhost为本集群名称**，9092为端口号，测试环境下写一个即可，生产环境则2-3个

```
./bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic name --create --partitions 1 --replication-factor 3
```

- --topic name	选中topic，name为topic名字，name可自定义更改
- --create	创建topic
- --partitions 1 	设置分区，1可以改为自定义分区
- --replication-factor 3	设置副本数，3可以改为自定义副本数

```
./bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic name --describe
```

- -topic name	选中topic，name为topic名字，name可自定义更改

- --describe	查询name topic的详细信息

##### zookeeper

```
bin/kafka-topics.sh --zookeeper localhost:2181 --topic name --create --partitions 1 --replication-factor 3
```

两个方法不一样的地方是再topic后面使用的指令，我现在使用的是`--zookeeper`，没有使用`--bootstrap-server`的原因是后者创建topic成功没有提示（……但是我就是要那个提示）

------

##### bootstrap和zookeeper的区别

我们使用的kafka版本为2.x版本，而**#--bootstrap-server**为kafka3.x版本的指令

Kafka开发团队重写了ZooKeeper的Quorum控制器代码并嵌入到Kafka中。所以从v2.8版本开始，Kafka不再依赖ZooKeeper。

所以，

旧版：

```
--zookeeper localhost:2181
```

新版：

```
--bootstrap-server localhost:9092
```

其中，2181是ZooKeeper的监听端口，9092是Kafka的监听端口。

旧版用--zookeeper参数，主机名（或IP）和端口用ZooKeeper的，也就是server.properties文件中zookeeper.connect属性的配置值

新版用--bootstrap-server参数，主机名（或IP）和端口用某个节点的即可，即主机名（或主机IP）:9092。

------

附表（需要的自取）：

|                kafka shell order                 |                           explain                            | 作用  |
| :----------------------------------------------: | :----------------------------------------------------------: | :---: |
|                     --alter                      | Alter the number of partitions,replication assignment,and/or configuration for the topic. |  改   |
| --bootstrap-server<String: server to connect to> |           REQUIRED:The Kafka server to connect to.           | 连接  |
|              --topic<String: topic>              | The topic to create, alter,describe or delete.It also accepts a regular expresstion,except for --create option.Put topic name in double quotes and use the ' \ ' prefix to escape regular expression symbols;  e.g."test \ .topic". | topic |
|                     --create                     |                     Create a new topic.                      |  增   |
|                     --delete                     |                        Delete a topic                        |  删   |
|                      --list                      |                  List all available topics                   |  查   |
|                    --describe                    |              List details for the given topics               |  查   |
|      --partitions<Integer: # of partitions>      | The number of partitions for the topic being created or altered(WARNING:If partitions are increased for a topic that has a key,the partition logic or ordering).If not supplied for create,defalts to the cluster default. | 分区  |
| --repliction-factor<Integer:replication factor>  | The replication factor for each partition in the topic being created.If not supplied,defaults to the cluster default. | 副本  |