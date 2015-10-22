---
layout: post  
title: 在centos6上安装thrift  
category: program  
tags: [thrift]  

---

我们将要安装thrift版本为 0.9.2


1、安装前环境准备--安装依赖

```
yum -y install  libtool flex pkgconfig gcc-c++ boost-devel  python-devel ruby-devel 
autoconf、automake、bison、libevent-devel zlib-devel openssl-devel

```

<b>使用上述安装bison，在安装thrift过程中会报错，bison版本过低，需要安装>2.5版本。</b>

```
wget http://ftp.gnu.org/gnu/bison/bison-2.5.1.tar.gz
tar xvf bison-2.5.1.tar.gz
cd bison-2.5.1
./configure --prefix=/usr
make
sudo make install
cd ..
```

2、使用java还需要安装

```
yum -y ant maven

```

3、编译安装

```
./configure --with-lua=no JAVAC=/usr/java/jdk1.7.0_79/bin/javac
make && make install

```

4、安装完成使用下列命令进行校验  
`thrift -version`


5、使用perl的接口
thrift -r --gen perl tutorial.thrift