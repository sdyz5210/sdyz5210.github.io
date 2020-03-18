---
layout: post  
title: docker基本命令
date: 2017-10-17 
tags: docker
--- 

### 常用命令


* 列出本地的docker镜像列表
* docker images

* 从远程拉取指定的镜像列表
* docker pull ubuntu:16.04

* 删除本地镜像
* docker rmi 【image id】

* 查看正在运行的容器
* docker ps

* 查看终止状态的容器
* docker ps -a

* 停止运行中的容器
* docker stop 【container id】

* 启动停止中的容器
* docker start 【container id】

* 删除容器
* docker rm 【container id】

* 进入容器
* docker attach 【container id】

* 启动并进入容器内
* docker run -it ubuntu:16.06 bash

* 启动并进入容器，退出是删除容器
* docker run -it --rm ubuntu:16.04 bash

### 构建自己的docker镜像

1、 基于ubuntu16.04构建自己的docker镜像，同时按照jdk环境，首先编写Dockerfile文件

```
# ubuntu 16.04 base
FROM ubuntu:16.04
MAINTAINER summer<sdyz5210gmail.com>

# install vim wget
RUN apt-get update && apt-get -y install vim wget

#set work dir
WORKDIR /usr/local

# download jdk
RUN wget --no-cookies --no-check-certificate --header "Cookie:gpw_e24=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk8-downloads-2133151.html;oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz

#install jdk
RUN tar -zxf jdk-8u144-linux-x64.tar.gz
#RUN rm -rf jdk-8u144-linux-x64.tar.gz

#set jdk env
ENV JAVA_HOME=/usr/local/jdk1.8.0_144
ENV PATH=$JAVA_HOME/bin:$PATH
ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

```

2、 构建

在Dockerfile当前目录构建：

` docker build -t sdyz5210/ubuntu16.04 .`

3、 提交到docker仓库

```
docker login --username=xxx --password=xxx
docker push sdyz5210/ubuntu:16.04
```

### 在nginx中运行swagger服务

1、 直接从仓库拉取

` docker pull nginx `

2、 启动部署nginx 

` docker run -it --rm nginx bin/bash `

3、将swagger-ui部署在nginx中，静态资源存在在宿主机器上

将静态目录映射到nginx的部署目录中

` docker run --name nginx-test -d -p 8080:80 -v /Users/mac/Documents/workspaces/dockers/swagger/swagger-ui-master/dist:/usr/share/nginx/html nginx`

4、将swagger-editer部署在nginx，静态资源存放在宿主主机上

`docker run --name nginx-test -d -p 8080:80 -v /Users/mac/Documents/workspaces/dockers/swagger/swagger-editor:/usr/share/nginx/html nginx`


### 基于nginx镜像，打包自己的swagger服务

Dockerfile文件的内容如下,前提条件是需要把swagger的资源下载到本地，放在Dockerfile同级目录下：

```
FROM nginx
#可以根据自己的需求进行nginx的配置，我这里使用默认配置
#ADD nginx.conf /etc/nginx/
ADD swagger-ui/dist /usr/share/nginx/html/swagger-ui
ADD swagger-editor /usr/share/nginx/html/swagger-editor

```