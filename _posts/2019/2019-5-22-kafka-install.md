---
layout: post
title: kafka在centos上安装
tags: [centos]

---

### 前言

系统中使用kafka\zookeeper很长一段时间了，一直没有梳理相关的知识，现在抽时间梳理并记录下来，供以后参考。
本文中为安装kafka。

### kafka简介

Apache Kafka是分布式发布-订阅消息系统。它最初由LinkedIn公司开发，之后成为Apache项目的一部分。Kafka是一种快速、可扩展的、设计内在就是分布式的，分区的和可复制的提交日志服务。

Apache Kafka与传统消息系统相比，有以下不同：
	* 它被设计为一个分布式系统，易于向外扩展；
	* 它同时为发布和订阅提供高吞吐量；
	* 它支持多订阅者，当失败时能自动平衡消费者；
	* 它将消息持久化到磁盘，因此可用于批量消费，例如ETL，以及实时应用程序。

### 环境配置

参考zookeeper的环境

### 具体安装过程

首先在master上进行安装，具体执行方法如下：

```

# 进去安装包所在目录  
cd /usr/local/software/
# 解压缩安装包
tar -zxvf kafka_2.11-2.2.0.tgz
# 移动并重命名安装包
mv kafka_2.11-2.2.0 ../kafka
# 进入安装目录
cd ../kafka

# 下面进行kafka的配置--主要配置

broker.id=0
listeners=PLAINTEXT://master:9092
log.dirs=/usr/local/celloud/data/kafka/logs/
zookeeper.connect=master:2181,node1:2181,node2:2181

```

配置完成后，需要把kafka文件夹拷贝到node1、node2节点上。

拷贝完成后需要修改一下两个配置,示例配置如下：

```

# node1上的配置
broker.id=1
listeners=PLAINTEXT://node1:9092

# node2上的配置
broker.id=2
listeners=PLAINTEXT://node2:9092

```

### kafka使用

```

# 分别在master、node1、node2上分别启动kafka
bin/kafka-server-start.sh config/server.properties

# 启动kafka后，新开命令行窗口进行一下的操作
# 创建topic
bin/kafka-topics.sh --create --zookeeper master:2181 --replication-factor 2 --partitions 2 --topic test

# 查看topic
bin/kafka-topics.sh --list --zookeeper master:2181

# 产生消息
bin/kafka-console-producer.sh --broker-list master:9092 --topic test

# 消费者--这里我在node2节点上进程消费的
# 注意在新版的kafka消费者端，不在需要zookeeper支持，需要指定：--bootstrap-server参数。
bin/kafka-console-consumer.sh --bootstrap-server node2:9092 --topic test --from-beginning

```