---
layout: post
title: ubuntu14.04安装MariaDB数据库
category: program 
tags: [mariadb]
---

#简介
随着mysql被oracle收购之后，很多企业和机构都纷纷选择逃离mysql。而mysql之父在原mysql的基础上开源了一个新的数据库Mariadb。MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可。MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。

#安装命令
安装的Mariadb版本为：10.0。此次安装直接使用ubuntu的命令进行安装，你也可以参考官方详细的安装步骤：https://downloads.mariadb.org/。
下面是在ubuntu14.04上面安装Mariadb的准备命令：

```
sudo apt-get install software-properties-common
sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xcbcb082a1bb943db
sudo add-apt-repository 'deb http://mirrors.neusoft.edu.cn/mariadb/repo/10.0/ubuntu trusty main'
```

上面执行成功后可以通过下面的命令直接安装即可：

```
sudo apt-get update
sudo apt-get install mariadb-server
```

#维护命令

```
sudo /etc/init.d/mysql start    //启动命令
sudo /etc/init.d/mysql stop     //停止命令
sudo /etc/init.d/mysql restart  //重启命令
```

#客户端
Mariadb还可供了客户端的支撑--HeidiSQL，下载地址：http://www.heidisql.com/download.php选择相应的版本安装即可。
不过该客户端只支持在window下面进行安装。