---
layout: post
title: 安装配置nginx  
category: program 
tags: [nginx]  
---

#安装说明#

系统环境：Linux Centos6.3_64
软件：nginx1.7.7
下载地址：http://nginx.org/en/download.html

#源码安装#

```
//首先下载nginx源文件
wget -c http://nginx.org/download/nginx-1.7.7.tar.gz
//确保系统安装了g++、gcc、openssl-devel、pcre-devel和zlib-devel软件
yum install gcc-c++
yum -y install zlib zlib-devel openssl openssl-devel pcre pcre-devel 
//首先解压缩
tar -zxvf nginx-1.7.7.tar.gz
//进入nginx文件夹,并且配置，指定安装位置：/usr/local/nginx,同时安装http_ssl_module模块
cd nginx-1.7.7
./configure --prefix=/usr/local/nginx --with-http_ssl_module
//编译并安装
make && make install
```

使用方式如下：

```
cd /usr/local/nginx/sbin
//启动
./nginx
//重启
./nginx -s reload
//停止
./nginx -s stop
```

#yum安装#
和使用源码安装相比，直接使用yum命令安装更加简单。
首先在centos中增加nginx的yum仓库，在`/etc/yum.repos.d/`目录中创建一个nginx.repo文件，并且增加以下内容：

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```
注意：系统的 Linux 定义了 $releasever 变量，需要根据你的操作系统的版本，将手动地将 $releasever 替换为 "5" （代表 5.x） 或 "6" （代表 6.x）。这里需要根据你安装的操作系统的版本进行选择，这里我们安装的时centos，所以这里的baseurl是：`baseurl=http://nginx.org/packages/centos/6/$basearch/`

配置好了yum源之后，直接使用以下命令安装即可：

```
yum install nginx
```

针对nginx的安装我们同样可以参考官网的说明：http://nginx.org/en/linux_packages.html。这里面有较为详细的说明。