---
layout: post
title: 在ubuntu16.04上安装docker
category: program
tags: [docker]
---

#ubuntu#
Docker可以安装在下列ubuntu的版本上：

* Ubuntu Xenial 16.04 (LTS)
* Ubuntu Wily 15.10
* Ubuntu Trusty 14.04 (LTS)
* Ubuntu Precise 12.04 (LTS)

#安装条件#

Docker需要安装在64位的操作系统上，内核的版本不能低于3.10,你可以使用命令`uname -r `查看ubuntu系统的内核.

#安装#

使用命令行执行相应的操作,注意需要使用root或者sudo的权限，首先需要更新源，然后安装https的支持，最后添加GPG key
具体命令操作如下：

```
$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
# 打开下面的文件，如果没有就创建
$ sudo vim /etc/apt/sources.list.d/docker.list
# 在文件中增加以下内容，根据不同的ubuntu版本添加不同的信息。
# 12.04
# deb https://apt.dockerproject.org/repo ubuntu-precise main
# 14.04
# deb https://apt.dockerproject.org/repo ubuntu-trusty main
# 15.10
# deb https://apt.dockerproject.org/repo ubuntu-wily main
# 16.04
# deb https://apt.dockerproject.org/repo ubuntu-xenial main

# 再次更新源
$ sudo apt-get update
# 清楚旧的docker的源
$ sudo apt-get purge lxc-docker
# 验证docker的正确的源
$ apt-cache policy docker-engine

# docker官方推荐安装linux-image-extra

$ sudo apt-get install linux-image-extra-$(uname -r)

#具体安装

$ sudo apt-get update
$ sudo apt-get install docker-engine
```

#启动与测试#

```
$ sudo service docker start
$ sudo docker run hello-world
```