---
layout: post
title: zookeeper在centos上安装
tags: [centos]
---

### 前言

系统中使用kafka\zookeeper很长一段时间了，一直没有梳理相关的知识，现在抽时间梳理并记录下来，供以后参考。

本次先按照zookeeper，kafka的安装在下一篇。

### Zookeeper简介

ZooKeeper（动物园管理员），顾名思义，是用来管理Hadoop（大象）、Hive（蜜蜂）、Pig（小猪）的管理员，同时Apache HBase、Apache Solr、LinkedIn Sensei等众多项目中都采用了ZooKeeper。ZooKeeper曾是Hadoop的正式子项目，后发展成为Apache顶级项目，与Hadoop密切相关但却没有任何依赖。它是一个针对大型应用提供高可用的数据管理、应用程序协调服务的分布式服务框架，基于对Paxos算法的实现，使该框架保证了分布式环境中数据的强一致性，提供的功能包括：配置维护、统一命名服务、状态同步服务、集群管理等。在分布式应用中，由于工程师不能很好地使用锁机制，以及基于消息的协调机制不适合在某些应用中使用，因此需要有一种可靠的、可扩展的、分布式的、可配置的协调机制来统一系统的状态。Zookeeper的目的就在于此。

Zookeeper的下载地址：https://zookeeper.apache.org/releases.html，注意下载稳定版本且是编译后的bin版本使用。

### 环境配置

准备了3台虚拟机进行构建集群模式，三台虚拟机分别配置了hostname和hosts映射：

master	192.168.1.100
node1	192.168.1.101
node2	192.168.1.102

master机器和node之间做了免密操作。

### 安装JDK

在Centos上安装jdk不在这里展示，不会的自行google。

### 安装zookeeper

首先在Master节点上进行安装。

解压缩zookeeper安装包，进入conf文件夹

```
# 进去安装包所在目录  
cd /usr/local/software/
# 解压缩安装包
tar -zxvf apache-zookeeper-3.5.5-bin.tar.gz
# 移动并重命名安装包
mv apache-zookeeper-3.5.5-bin ../zookeeper
# 进入安装目录
cd ../zookeeper

# 设置配置文件
cp config/zoo_sample.cfg config/zoo.cfg

# 编辑配置文件,修改里面的配置如下(dataDir的目录需要提前创建)：

tickTime=2000
initLimit=10
syncLimit=5
dataDir=/data/zookeeper
clientPort=2181

server.1=master:2888:3888
server.2=node1:2888:3888
server.3=node2:2888:3888

```

配置完成，把该文件夹分发到node1、node2两个节点上。

最后在三台机器的数据目录（/data/zookeeper）下创建并配置myid文件，填写内容为0：vi myid (内容为服务器标识： 0)。

### 启动和关闭命令

```

./bin/zkServer.sh start
./bin/zkServer.sh stop
./bin/zkServer.sh restart
./bin/zkServer.sh status

```