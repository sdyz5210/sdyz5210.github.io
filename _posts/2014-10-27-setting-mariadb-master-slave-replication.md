---
layout: post
title: MariaDB主从复制
category: program 
tags: [mariadb]
---

#前言#
随着系统数据量的增加，单台的数据库系统有时候不能满足实际需求，无论是在安全性、高可用性及高并发等方面。
1、可以作为一种备份机制，相当于热备份。主服务器挂掉，从服务器可以提供服务
2、可以做读写分离，均衡数据库负载。
MariaDB的安装可以参考：[MariaDB安装](http://fee.im/2014/10/23/centos6.3-install-mariadb5.5.40)
Mysql的安装可以参考：[Mysql安装](http://fee.im/2014/08/20/centos6.4-mysql5.5-install)
#主从复制（全部复制）#
##环境##
整个系统的配置都是在centos6.3操作系统下进行，机器分别为：192.168.1.100和192.168.1.101
主服务器：192.168.1.100
从服务器：192.168.1.101
主从服务器都已经安装上了MariaDB5.5.40，root初始密码为：123abc

##主服务器配置##
```java:n
 #打开配置文件
vim /etc/my.cnf

 #编辑内容
 [mysqld]
 log-bin=mysql-bin   //[必须]启用二进制日志
 server-id=1         //[必须]服务器唯一ID，默认是1
 
 #保存退出
 #重启服务
 service mysql restart
 
 #进入控制台
 mysql -uroot -p123abc
 
 MariaDB [(none)]> GRANT REPLICATION SLAVE ON *.* to 'root'@'192.168.1.101' identified by '123abc';
 Query OK, 0 rows affected (0.00 sec)

 MariaDB [(none)]> flush privileges;
 Query OK, 0 rows affected (0.00 sec)

 MariaDB [(none)]> show master status;
 +------------------+----------+--------------+------------------+
 | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
 +------------------+----------+--------------+------------------+
 | mysql-bin.000005 |      650 |              |                  |
 +------------------+----------+--------------+------------------+
 1 row in set (0.00 sec)

```
记录下 File 及 Position 的值，在后面进行从服务器操作的时候需要用到。

##从服务器配置##
```java:n
 #打开配置文件
 vim /etc/my.cnf
 
 #编辑内容
 [mysqld]
 log-bin=mysql-bin   //[必须]启用二进制日志
 server-id=2         //[必须]服务器唯一ID,这里要和主服务器区分开，我们设置2
 
 #保存退出
 #重启服务
 service mysql restart
 
 #进入控制台
 mysql -uroot -p123abc
 
 #停止slave服务进程
 MariaDB [(none)]> slave stop;
 Query OK, 0 rows affected (0.00 sec)
 
 #正确执行后启动Slave同步进程
MariaDB [(none)]> change master to master_host='192.168.1.100',master_user='root',master_password='123abc',master_log_file='mysql-bin.000005',master_log_pos=650;
Query OK, 0 rows affected (0.01 sec)

MariaDB [(none)]> slave start;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: 
                  Master_Host: 192.168.1.100
                  Master_User: root
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000005
          Read_Master_Log_Pos: 650
               Relay_Log_File: localhost-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000005
             Slave_IO_Running: No
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 650
              Relay_Log_Space: 245
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 1593
                Last_IO_Error: Fatal error: The slave I/O thread stops because master and slave have equal MySQL server ids; these ids must be different for replication to work (or the --replicate-same-server-id option must be used on slave but this does not always make sense; please check the manual before using it).
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 1
1 row in set (0.00 sec)

```
其中Slave_IO_Running 与 Slave_SQL_Running 的值都必须为YES，才表明状态正常。

#主从复制（选择性复制）#
选择性复制的配合是在全部复制的基础上进行的，这里我们只需要了解两个配置参加即可：binlog-do-db和binlog-ignore-db、replicate-do-db和replicate-ignore-db。
##主服务器配置##
该配置在上述全部复制的基础上增加以下内容：

```
binlog-do-db=fee
binlog-do-db=summer
binlog-ignore-db=mysql
binlog-ignore-db=test
binlog-ignore-db=information_schema
binlog-ignore-db=performance_schema
```

binlog-do-db是指在主服务器上配置允许同步的数据库。
binlog-ignore-db是指在主服务器上配置不允许同步的数据库。
一般数据库系统自带的数据库我们都选择不同步。

##从服务器配置##
该配置同样是在全部复制的基础上增加以下内容：

```
replicate-do-db = fee
replicate-do-db = summer
replicate-ignore-db=mysql
replicate-ignore-db=test
replicate-ignore-db=information_schema
replicate-ignore-db=performance_schema
```

replicate-do-db配置允许从服务器from主服务器上同步的数据库
replicate-ignore-db配置禁止从服务器from主服务器上同步的数据库

#问题#
1、CentOS防火墙配置了吗？
开始进行配置主从复制的时候，一直都不成功，最后才发现两个机器的防火墙的原因，CentOS默认防火墙是开启的，我们可以关闭防火墙`service iptables stop`。但是在实际应用中我们还是不建议关闭防火墙，只需要在防火墙中配置一下端口转发规则即可。

2、我安装完成MariaDB后，my.cnf配置文件和Mysql下面的配置文件相差很大是怎么回事？
MariaDB的配置文件主要是my.cnf.d目录下面，分为：server.cnf、mysql-clients.cnf等，关于这块内容的解释如果有了解的朋友可以直接给我留言，本人也是开始接触MariaDB数据库，这地方的配置信息也没用了解完整。
在这里本人取巧进行了处理，直接拷贝的mysql自带的配置文件(`cp /usr/share/mysql/my-innodb-heavy-4G.cnf /etc/my.cnf`)，覆盖了mariadb安装时的my.cnf，之前最好备份一下.

解决：MariaDB最新版的配置文件分为server.cnf、mysql-clients.cnf,前期不知道怎么配置，最近尝试了一下，以前在使用mysql的时候，我们一般都是修改/etc/my.cnf配置文件，所以到了Mariadb我们就迷茫了，根据MariaDB的描述我们对my.cnf的操作，可以调整为对server.cnf的操作，或者我们可以理解成my.cnf拆分成了两个配置文件server.cnf、mysql-clients.cnf，我们大部分的配置工作都是在server.cnf中进行即可，包括设置主从复制，修改默认编码等等。


