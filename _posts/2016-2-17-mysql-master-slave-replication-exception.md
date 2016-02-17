---
layout: post
title: MariaDb主从复制失败
category: program
tags: [MariaDb]
---

为系统设置数据库的主从复制，今天突然发现数据库在某个时间点报错后不再继续同步，需要处理一下使之继续工作。
在slave上使用`show slave status\G `查看一下同步的状态，发现同步出现错误。

从网上找了两种解决方式

#方法一 

该方法适用于主从库数据相差不大，或者要求数据可以不完全一致的情况，即数据要求不严格的情况。

在从服务器上执行：

```
stop slave

#表示跳过一步错误，后面的数字可变
set global sql_slave_skip_counter = 1

start slave

```
之后再使用`show slave status\G`查看即可

```
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```
ok，现在主从同步状态正常了

#方法二 

该方法适用于主从数据库相差较大，或者对数据一致性要求比较高的情况

* 首先先进入主库，进行锁表操作，放置数据写入

```
flush tables with read lock;
#该处锁定位只读状态，语句不区分大小写

```
* 进行数据备份

```
mysqldump -uroot -p -hlocalhost > test.sql
```

* 查看master状态

```
mysql> show master status;
+-------------------+----------+--------------+-------------------------------+
| File              | Position | Binlog_Do_DB | Binlog_Ignore_DB              |
+-------------------+----------+--------------+-------------------------------+
| mysqld-bin.000005 |     55260 |              | mysql,test,information_schema |
+-------------------+----------+--------------+-------------------------------+
1 row in set (0.00 sec)

```

* 使用scp命令把备份的脚本拷贝到从服务器上

* 操作从服务器

```
#先停止同步
stop slave
#导入数据
source test.sql
#设置从库同步
change master to master_host = '192.168.100.123', master_user = 'root', master_port=3306, master_password='', master_log_file = 'mysqld-bin.000005', master_log_pos=55260;
#开启同步
start slave;
```

* 查看同步状态

```
show slave status\G

Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```
ok，现在主从同步状态正常了

* 恢复主服务器

```
unlock tables; 解锁
```













