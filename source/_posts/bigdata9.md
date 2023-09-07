---
title: Redis
date: 2023-07-15 17:20:02
tags:
categories: 大数据集群搭建
---
## Redis

**解压文件**

```
tar -zxvf /opt/software/redis.tar.gz -C /opt/module/
```

**查看Linux系统有没有gcc环境**

```
gcc -version
```

**安装gcc**

```
yum install gcc-c++
```

**编译redis**

```
cd /opt/module/redis-6.2.6
```

```
make
```

**安装redis**

```
make install PREFIX=/opt/module/redis-6.2.6
```

**拷贝conf**

```
cp redis.conf /opt/moduele/redis-6.2.6/bin
```

**启动redis**

- 前台启动

```
./bin/redis-server redis.conf
```

- 直接运行的时候设置参数

```
./bin/redis-server --port 6388
```

- 后台启动，使用配置文件

> 备份 redis.conf , 拷贝一份 redis.conf 到其他目录。 如让在 /etc 目录下，cp redis.conf /etc/redis.conf
> 修改配置文件， 将 daemonize no 改成 yes， 让服务在后台启动
> redis 后台启动： redis-server /etc/redis.conf

- 将启动固定为启动命令

```
# cat /usr/lib/systemd/system/redis.service
[Unit]
Description=Redis
After=syslog.target network.target remote-fs.target nss-lookup.target
 
[Service]
Type=forking
PIDFile=/run/redis_6379.pid
ExecStart=/usr/local/redis-6.2.4/src/redis-server /usr/local/redis-6.2.4/redis.conf
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true
 
[Install]
WantedBy=multi-user.target
```

**查看redis进程**

```
ps -ef | grep redis
```

