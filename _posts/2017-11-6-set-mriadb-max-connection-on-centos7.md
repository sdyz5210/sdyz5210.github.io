---
layout: post  
title: Centos7.3 设置mariadb的默认最大连接数 
date: 2017-11-06 
tags: [mariadb]  

--- 

### 1、查看mariadb数据库最大连接数  

```
MariaDB [(none)]> show variables like 'max_connections';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+
1 row in set (0.00 sec)

MariaDB [(none)]>

```

### 2、配置/etc/my.cnf.d/servce.conf

[mysqld]新添加一行如下参数：
max_connections=1000
重启mariadb服务，再次查看mariadb数据库最大连接数，可以看到最大连接数是214，并非我们设置的1000。

```
MariaDB [(none)]> show variables like 'max_connections';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 214   |
+-----------------+-------+
1 row in set (0.00 sec)

MariaDB [(none)]>

```

这是由于mariadb有默认打开文件数限制。可以通过配置/usr/lib/systemd/system/mariadb.service来调大打开文件数目。

```
vim /usr/lib/systemd/system/mariadb.service

#在service下面增加两行参数
LimitNOFILE=10000
LimitNPROC=10000

```

### 3、重启mairadb服务即可。

```
systemctl restart mariadb.service 

#再次查看mairadb的参数。
MariaDB [(none)]> show variables like 'max_connections';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 1000  |
+-----------------+-------+
1 row in set (0.00 sec)

MariaDB [(none)]>

```