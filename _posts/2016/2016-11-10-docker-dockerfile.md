---
layout: post
title: 通过dockerfile制作image并提交
category: program
tags: [docker]
---

尝试学习使用dockerfile构建docker容器，并上传构建的容器到公有库。中间参考了不少网友的[博文](http://blog.csdn.net/qinyushuang/article/details/43342553)，

1、首先编写Dockerfile。

```
FROM ubuntu:16.04

MAINTAINER sdyz5210@gmail.com

RUN sed -i 's/^#\s*\(deb.*universe\)$/\1/g' /etc/apt/sources.list
RUN apt-get update
RUN apt-get -y install curl

RUN cd /tmp &&  curl -L 'http://download.oracle.com/otn-pub/java/jdk/7u65-b17/jdk-7u65-linux-x64.tar.gz' -H 'Cookie: oraclelicense=accept-securebackup-cookie; gpw_e24=Dockerfile' | tar -xz
RUN mkdir -p /usr/lib/jvm
RUN mv /tmp/jdk1.7.0_65/ /usr/lib/jvm/java7/

RUN update-alternatives --install /usr/bin/java java /usr/lib/jvm/java7/bin/java 300
RUN update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/java7/bin/javac 300
ENV JAVA_HOME /usr/lib/jvm/java-7-oracle/

RUN cd /tmp && curl -L 'http://archive.apache.org/dist/tomcat/tomcat-7/v7.0.8/bin/apache-tomcat-7.0.8.tar.gz' | tar -xz  
RUN mv /tmp/apache-tomcat-7.0.8/ /opt/tomcat7/
  
ENV CATALINA_HOME /opt/tomcat7
ENV PATH $PATH:$CATALINA_HOME/bin
  
ADD tomcat7.sh /etc/init.d/tomcat7
RUN chmod 755 /etc/init.d/tomcat7
  
# Expose ports.  
EXPOSE 8080
  
# Define default command.  
ENTRYPOINT service tomcat7 start && tail -f /opt/tomcat7/logs/catalina.out

```
设置tomcat的启动、关闭命令

```
export JAVA_HOME=/usr/lib/jvm/java7/  
export TOMCAT_HOME=/opt/tomcat7  

case $1 in  
start)  
  sh $TOMCAT_HOME/bin/startup.sh  
;;  
stop)  
  sh $TOMCAT_HOME/bin/shutdown.sh  
;;  
restart)  
  sh $TOMCAT_HOME/bin/shutdown.sh  
  sh $TOMCAT_HOME/bin/startup.sh  
;;
esac  
exit 0

```

开始构建

```
docker build -t sdyz5210/jdk-tomcat .
#映射docker的端口8080到主机的端口8090上。
docker run -d -p 8090:8080 sdyz5210/jdk-tomcat
docker images
#关闭容器
docker stop $ID

```
接下来就可以通过浏览器（http://localhost:8090）访问到tomcat了

```
step1——找到本地镜像的ID：docker images
	获取到ID:ec22f518562a
step2——登陆Hub：docker login --username=username --password=password --email=email
	docker login --username=sdyz5210 --password=***** --email=****@gmail.com
step3——tag：docker tag <imageID> <namespace>/<image name>:<version tag eg latest>
	docker tag ec22f518562a sdyz5210/tomcat:beta
step4——push镜像：docker push <namespace>/<image name>
	docker push sdyz5210/tomcat

```