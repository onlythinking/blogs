---
title: Spring boot 2.x Kafka 简易教程
date: 2019-10-10 11:18:31
tags: 
	- zookeeper
	- Kafka
categories: 框架
copyright:
typora-root-url: ../
typora-copy-images-to: ../images
---

#### 前言

​	本文属简易入门教程，高手请忽略该文，文中有存在的错误，或表述不当欢迎👏👏指出。

#### 基本概念

​	面对海量互联网访问需求，单一服务器处理达到极限，就出现利用多台服务器分摊负载的策略，这就是简单的分布式概念。

分布式运行也带来的很多复杂性，就像一个人干活和一群人干活，一群人干活就需要介入管理者来协调运作，不然出现错误，效率反倒不如一个人干活。

分布式其中一个要解决的问题就是一致性问题（数据存储一致性，操作原子性等）。

#### Zookeeper

##### 概述

Zookeeper就是用来协调分布式应用程序的分布式协调服务。

Zookeeper 为提高可靠性，也支持集群，结构如下：

zookeeper内部所有server内数据都相同，其中有一个leader，当client请求连接zookeeper时，zookeeper内部会根据各自server压力分配连接，对于client来说不关心具体连接到那个server，只需知道是否连接上zookeeper。

![ZooKeeper服务](/images/kfk-1.jpg)



##### 模型分层名称空间

zookeeper 通过共享的分层名称空间相互协调，存储协调数据，如：状态信息，配置，位置信息等。另外它的数据的保留在内存。

![ZooKeeper的层次命名空间](/images/kfk-2.jpg)

##### 应用场景

命名服务 配置管理 集群管理 节点领导者选举 锁定和同步服务 数据注册表

##### 应用案例

Hadoop，Hbase，Dubbo，Kafka 等

更多关于zookeeper内容，[请查看官方文档](https://zookeeper.apache.org/doc/current/zookeeperOver.html)

##### 安装配置

选择需要的版本：[下载地址](http://zookeeper.apache.org/releases.html)

单例模式下，添加配置文件conf/zoo.cfg

```bash
#心跳时间
tickTime=2000
dataDir=/var/lib/zookeeper
#client连接端口
clientPort=2181
```

```bash
#启动服务
$ bin/zkServer.sh start
```

```bash
#连接zk
$ bin/zkCli.sh -server 127.0.0.1:2181
```

```bash
[zkshell: 0] help
ZooKeeper host:port cmd args
    get path [watch]
    ls path [watch]
    set path data [version]
    delquota [-n|-b] path
    quit
    printwatches on|off
    create path data acl
    stat path [watch]
    listquota path
    history
    setAcl path acl
    getAcl path
    sync path
    redo cmdno
    addauth scheme auth
    delete path [version]
    deleteall path
    setquota -n|-b val path
```

集群模式下配置，配置文件conf/zoo.cfg添加以下内容，分别在dataDir目录下面创建myid文件，文件值为server.{数值}的数值。

```bash
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
#Leader-Follower初始通信超时时间（initLimit * tickTime）= 10s
initLimit=5
#Leader-Follower同步通信超时时间（syncLimit * tickTime）= 4s
syncLimit=2
#server.实例集群标识=实例地址:数据通信端口:选举通信端口
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
```



#### Kafka

##### 概念

- 发布和订阅记录流，类似于消息队列或企业消息传递系统。
- 以容错的持久方式存储记录流。
- 处理记录流。

<img src="/images/kfk-3.png" alt="img" style="zoom:67%;" />

从结构层面来看，一个主题下面可以有多个分区，每个分区中的记录都分配一个**偏移的顺序ID号**。

![img](/images/kfk-40.png)

从处理层面来看，生产者负责选择将哪个记录分配给主题中的哪个分区， 每个消费者保留的唯一元数据是该消费者在日志中的偏移量或位置。

![img](/images/kfk-5.png)



从分区日志存储上看，每个分区都在可配置数量的服务器之间复制，以实现容错功能。

每个分区都有一个充当“领导者”的服务器和零个或多个充当“跟随者”的服务器。领导者处理对分区的所有读写请求，而跟随者则被动地复制领导者。如果领导者失败，则跟随者之一将自动成为新领导者。每个服务器充当其某些分区的领导者，而充当其他分区的跟随者，因此群集中的负载得到了很好的平衡。

##### 搭建Kafka集群(2台)

选择需要的版本：[下载地址](http://us.mirrors.quenda.co/apache/kafka/)

![img](/images/kfk-6.png)

这里选择2.2.1版本

Kafka依赖zookeeper，保证zookeeper已启动。

修改对应配置文件：config/server.properties

```bash
#节点标识
broker.id=1
#服务端口
listeners=PLAINTEXT://:9092
log.dirs=/var/logs/kafka-logs/
# zk集群地址
zookeeper.connect=zoo1:2181,zoo1:2182,zoo1:2183
```

```bash
#节点标识
broker.id=2
#服务端口
listeners=PLAINTEXT://:9092
log.dirs=/var/logs/kafka-logs/
# zk集群地址
zookeeper.connect=zoo1:2181,zoo1:2182,zoo1:2183
```

```bash
#分别启动服务
$ bin/kafka-server-start.sh config/server.properties
```

##### 主题管理

```bash
$ bin/kafka-topics.sh --create \
--zookeeper zoo1:2181,zoo1:2181,zoo1:2181 \
--replication-factor 2 \ # 副本集数量，跟broker节点数量保持一致
--partition 4 \ # 分区数
--topic test
```

```bash
#查看主题列表
$ bin/kafka-topics.sh \
--zookeeper zoo1:2181,zoo1:2181,zoo1:2181 --list
```

```bash
#查看主题详情
$ bin/kafka-topics.sh \
--zookeeper zoo1:2181,zoo1:2181,zoo1:2181 \
--describe --topic test
```

##### 生产者

```bash
$ bin/kafka-console-producer.sh \
--broker-list kafka1:9092,kafka2:9092 \
--topic test
#输入消息，回车发送
```

##### 消费者

```bash
#创建消费者
$ bin/kafka-console-consumer.sh \
--new-consumer \
--bootstrap-server kafka1:9092,kafka1:9092 \
--from-beginning \
--topic test
```



#### Spring boot 集成Kafka

##### 版本兼容

注意 spring-boot ---> spring-kafka ---> Kafka-client 之间的版本依赖。版本不对会有意外的惊喜。

![img](/images/kfk-7.png)

引入Spring-boot和Spring-kafka依赖

![img](/images/kfk-8.png)

![img](/images/kfk-90.png)

application.yml 添加配置

![img](/images/kfk-10.png)

![img](/images/kfk-11.png)