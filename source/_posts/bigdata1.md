---
title: README
date: 2023-06-29 18:08:12
tags:
categories: 大数据集群搭建
---
[TOC]



## 前言

本章讲解的是在搭建Hadoop生态系统前需要的一些思路和条件，内容冗余，多是些吐槽的话，大概看看内容是否都已经配置了即可。

Hadoop 生态是学习大数据的重中之重，虽然我理解不深，但是关于hadoop所衍生出的apache的顶级开源项目，都是围绕着hadoop而进行使用的，所以要想使用apache的其他顶级项目，首先要理解的就是关于Hadoop的一些知识。

由于我在学习的时候，是采用实例式学习，于是乎，先是会用，才是理解项目，所以有很多东西，是自己的理解，不能和真正的学院派相媲美，所以此文章更多的是用于自己搭建训练的时候使用，一些问题也是相关与自身问题而撰写，分门别类，各位读者各取所需即可

（EagleHawk 2023.6.28）





## 前置安装

### 搭建规划

写在最前——是因为写到第三点了，结果发现规划应该是坐在所有步骤之前的，好吧只能写在最前了TAT

如何规划集群搭建：

首先要了解hadoop的工作原理。

hadoop有很多子项目，在规划时，我（个人哈o(*￣▽￣*)ブ）只考虑两个模块，一个是HDFS分布式文件系统，另一个是MapReduce分布式计算框架。这两个子项目是hadoop当中相当吃资源的两个项目，所以一般是需要讲这两个模块部署在不同的节点上面（即，hdfs部署在一台虚拟机上，mapreduce部署在一台虚拟机上）。

以上，就是简单的搭建规划了，在搭建规划时，我们还需要规划ip名，ip地址等，这里就不赘述了，直接实操就能理解（好吧，因为我也是实操练出来的...拳打百遍，其意自现嘛hhh）以下，是个人的部分理解。

------

HDFS存储架构下，有三个进程：NameNode，DataNode，SecondaryNameNode

MapReduce计算框架下，有两个进程：ResourceManger，NodeManager

其中SecondaryNameNode和ResourceManger都是占资源的大户，所以一般来说，会将这两个进程部署在不同的节点，作为完全分布式的部署。如果将所有进程都部署在相同节点下，则是伪分布式的安装配置。

| 节点名称 |    IP规划     |     分布式规划     |
| :------: | :-----------: | :----------------: |
|  master  | 192.168.xx.xx | NameNode、DataNode |
|  slave1  | 192.168.xx.xx | SecondaryNameNode  |
|  slave2  | 192.168.xx.xx |  ResourceManager   |

分析好了节点分布，那么就可以开始部署环境了——(ง •_•)ง

### 环境条件

|   名称    |     媒介     |               版本                |                    备注                     |
| :-------: | :----------: | :-------------------------------: | :-----------------------------------------: |
|   Linux   |    虚拟机    |              CentOS7              | 3台（因为搭建的是集群，所以需要三台虚拟机） |
|    jdk    | tar.gz压缩包 |    jdk-8u212-linux-x64.tar.gz     |    jdk版本需要对应，可以进apche官网查看     |
|  hadoop   | tar.gz压缩包 |        hadoop-3.1.3.tar.gz        |                                             |
| zookeeper | tar.gz压缩包 | apache-zookeeper-3.5.7-bin.tar.gz |      zookeeper在配置HadoopHA时需要使用      |

在hadoop3.1.3版本之前，我进行过hadoop2.7版本的搭建，相比之下，hadoop版本更改了一些配置文件，同时也增加了一些需要配置的内容，在下文中会详细进行简述，但是大差不差，所有的配置都有内部的核心文件与参数，更改这些核心参数，就是集群搭建的核心。

### 虚拟机

在配置开始前，需要对每一台虚拟机做三个文件的了解或是配置

**hostname**

```
vi /etc/hostname
```

```
master
```

```
slave1
```

```
slave2
```

此文件存储着本机的ip名，可以进行修改，修改方式按照规划集群来进行修改，主节点master，分节点slave1，slave2。

------

**ifcfg-ens33**

```
vi /etc/sysconfig/network-script/ifcfg-ens33
```

> TYPE="Ethernet"（网络类型：英特网）
> BOOTPROTO="static"（网络配置参数：静态IP/dhcp 动态IP/none 无）
> NAME="ens33"（网络属于网卡：ens33）
> DEVICE="ens33"（网络名称：ens33）
> ONBOOT="yes"（开机自启动：yes/no）
> IPADDR="192.168.48.200"（本机IP）
> PREFIX="24"（子网掩码的位数）
> GATEWAY="192.168.48.2"（网关）
> DNS1="192.168.48.2"（域名）

这个文件是存储本机ip地址的文件，在不同的版本中，存储位置也会有变动，目前我见过的存储位置就两个文件，这个文件是OS7当中的存储位置，修改ip即在此文件中进行修改。

查询ip地址也可以用以下指令。

**查询ip**

```
ip addr
ifconfig
```

如果第二条指令报错为未安装工具包，安装命令

**安装net-tools工具包**

```
yum -y install net-tools
```

------

**hosts**

```
vi /etc/hosts
```

```
192.168.xx.xxx master
192.168.xx.xxx slave1
192.168.xx.xxx slave2
```

存放各个节点映射，ip名为本机ip名，ip地址为本机ip地址。

------

**注意**

修改完上述文件后，需要重启网络服务，重启机器

**重启网络服务**

```
service network restart
```

**重启机器**

```
reboot
```

### 防火墙

关于防火墙的问题，在进行集群搭建的时候，防火墙是一定需要关闭的东西。因为搭建的集群都是内网搭建的，对外还有一个服务器，那个服务器有防火墙，由它来访问内网集群，如果内网内开启防火墙，内网集群通讯会出现很多问题。关闭防火墙，一劳永逸！

这是在测试环境下需要关闭防火墙，在生产环境目前还没有进行过生产，所以只针对与当前测试版本

另外，以下shell代码都是在CentOS7版本下的shell命令，如果版本不同，请移步baidu.com

**查看防火墙状态**

```
systemctl status firewalld
```

**暂时关闭防火墙**

```
systemctl stop firewalld
```

**永久关闭防火墙**

```
systemctl disable firewalld
```

**重启防火墙**

```
systemctl enabled firewalld
```

### SSH

SSH是一种加密的网络传输协议，可以在不安全的网络中为网络服务提供安全的传输环境。SSH通过在网络中建立安全隧道来实现SSH客户端与服务器之间的连接。SSH最常见的用途是远程登录系统，人们通常利用SSH来传输命令行界面和远程执行命令。SSH使用频率最高的场合是类Unix系统，但是Windows操作系统也能有限度地使用SSH。2015年，微软宣布将在未来的操作系统中提供原生SSH协议支持，Windows 10 1803版本已提供OpenSSH工具。在设计上，SSH是Telnet和非安全shell的替代品。

通过SSH Client我们可以连接到运行了SSH Server的远程机器上。

而每台机器都可以生成自己的安全密钥，而我们在搭建集群时，为了方便ssh的服务切换，会进行ssh的免密登录。以此来快速进行SSH Server切换。

如果不考虑特殊情况，可以直接看免密登录的方式，其他作为了解即可。

------

**查看SSH服务是否启动**

```
systemctl status sshd
```

> @Action:active(running)SHH运行中
>
> @Connection refusedSSH未安装

**启动SSH服务**

```
systemctl start sshd
```

**安装OpenSSH Server**

```
sudo apt-get install openssh-server
```

------

接下来介绍SSH Client公共密钥登录，其中，-p port为监听的端口号，一般为22，如果为22，可省略；user为用户名，ip可以时IP、域名、别名

**查询用户名**

```
whoami
```

**SSH公共钥登录**

```
ssh -p 22 user@ip
ssh user@ip -p port
```

------

直接跳转到重点——**免密登录**，这个是我们搭建集群需要做的一个关键步骤，可以极大的方便我们进行节点的切换、拷贝、部署，所以必须要掌握，也推荐使用。

**生成SSH密钥**

```
ssh-keygen
```

密钥生成后，存储位置为：

> ~/.ssh/id_rsa.pub	公钥存储地址
>
> ~/.ssh/id_rsa			私钥存储地址

**复制密钥到分节点中**

```
ssh-copy-id master
ssh-copy-id user@ip -p port
```

**Mac安装ssh-copy-id**

```
brew install ssh-copy-id
```

此条用于没有安装ssh-copy-id命令时，使用的安装命令，仅限于Mac（额，和这个搭建没什么关系，就是一个补充的说明）

**Windows手动指令**

```
ssh user@ip -p port ‘mkdir -p .ssh&& cat >> .ssh/authorized_keys’ < ~/.ssh/id_rsa.pub
```

或者手动操作，复制公钥内容，然后登入远程机器，粘贴到

> ~/.ssh/id_rsa.pub 公钥存储地址
>
> .ssh/authorized_keys存储其他公钥的位置

**SSH配置别名**

```
vi ~/.ssh/config
```

```
Host lab
HostName remote
User user
Port port
```

个人目前没有使用过，但是在ssh其他服务当中有介绍ssh别名的使用方法，需要的话也可以记一下。

------

完成了免密登录，接下来介绍一下**SSH传输文件**服务，使用此命令可以极大的提高文件在集群内传输的效率——前提是需要做好上面的步骤。

**传输文件夹**

```
scp -r /path/file-titele/file user@ip:/path/file-titele
```

**使用别名**

```
scp port /path/to/local/file lab:/path/to/local/file
```

**将远程文件下载到本地**

```
scp lab:port /path/to/local/file /path/to/local/file
```

**将本地文件传输到远程**

```
scp -P port /path/to/local/file user@ip：/path/to/local/file
```

其中，port为端口号，path为路径，file为文件名，传输时如果传输同一地址，在被传输的文件夹下，传输路径是传输者的上一级文件（因为文件还没传过去hhh,这句话就是废话）

以上使用的方法都大差不差，主要的区别就是修改了参数达成不同的效果，如果要详细了解各参数的含义，可以搜索以下，这里我就只贴张图，不做其他说明，需要的自取就好啦。

![bd1](bd1.png)

### JDK

因为Hadoop的生态环境，都是以Java为底层逻辑编写的，所以在配置环境前，需要做的一个必要配置，就是配置JDK。这是配置Hadoop的前置要求，也是Hadoop集群生态圈的底层逻辑。

**解压文件到指定目录**

```
tar -zxvf /opt/software/jdk.tar.gz -C /opt/module/
```

**修改环境变量**

```
vi /etc/profile
```

```
export JAVA_HOME=/opt/module/jdk
export PATH=$PATH:$JAVA_HOME/bin
```



## 结语

