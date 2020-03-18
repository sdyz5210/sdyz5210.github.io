---
layout: post
title: Maven基本操作
category: program
tags: [maven]
---

本文只是记录在使用maven过程中常用的命令，不做为详细的maven教程

#创建一个web项目

```
mvn archetype:generate -DgroupId=com.nova.dr -DartifactId=dr -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

#创建一个java项目

```
mvn archetype:generate -DgroupId=com.nova.dr -DartifactId=dr -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

#转成eclipse项目 

java项目  `mvn eclipse:eclipse`  
web项目  `mvn eclipse:eclipse -Dwtpversion=2.0`

wtp(Web Tools Platform)开发J2EE Web应用程序的工具集

#问题

##Generating project in Batch mode

我们在执行创建java web项目时，有时会比较顺利，有时走到该问题的阶段时就卡住了，无法继续进行。可以在执行创建命令时，增加 `-X`参数查看具体内容，如下：

```
mvn archetype:generate -DgroupId=com.nova.dr -DartifactId=dr -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false -X 
```

我们可以看到程序停到了下面的位置,这可能由于网络或者其他原因造成的。

```
[DEBUG] Searching for remote catalog: http://repo1.maven.org/maven2/archetype-catalog.xml
```

解决办法很简单：

```
1. 把上述地址复制到浏览器中，下载这个文件到本地。
2. 把文件archetype-catalog.xml复制到目录.m2\repository\org\apache\maven\archetype\archetype-catalog\2.2下面。
3. 在上述命令后增加参数-DarchetypeCatalog=local，变成读取本地文件即可。
```

##指定JDK和编码

在pom.xml中的build元素下面，增加子元素，添加的内容如下：

```
<plugins>
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-compiler-plugin</artifactId>
		<version>3.1</version>
		<configuration>
			<source>1.7</source>
			<target>1.7</target>
		</configuration>
	</plugin>
</plugins>

```

注意，因为我使用的maven版本为3.2.5，所以这里maven插件的版本对应的是3.1，不同的maven版本对应的插件的版本可能不一样，maven插件的版本号和maven版本号是不一致的。
