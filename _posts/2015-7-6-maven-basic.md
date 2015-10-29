---
layout: post
title: Maven基本操作
category: program
tags: [maven]
---

本文只是记录在使用maven过程中常用的命令，不做为详细的maven教程

#创建一个web项目
mvn archetype:generate -DgroupId=com.nova.dr -DartifactId=dr -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false

#创建一个java项目
mvn archetype:generate -DgroupId=com.nova.dr -DartifactId=dr -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

#转成eclipse项目 

mvn eclipse:eclipse or web项目 mvn eclipse:eclipse -Dwtpversion=2.0
wtp(Web Tools Platform)开发J2EE Web应用程序的工具集
