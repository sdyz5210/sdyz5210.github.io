---
layout: post
title: Tomcat7开启Gzip压缩
category: program 
tags: [tomcat]
---

今天系统突然响应慢了很多，所以就考虑对目前的系统进行优化，底层mysql开启慢查询日志的记录，同时使用一些测试的工作对网站的性能进行测试。我们使用chrome浏览器进行分析加载速度的时候发现很多没有注意到的细节，这是我们以后运维需要加强学习的地方。我们发现有很多css和js文件都100多kb，造成资源加载的时候会非常慢，同时使用一些在线工具eg阿里测等进行监测，我们确实有很多工作可以做，例如开启tomcat的gzip压缩。

开启tomcat的gzip压缩需要在tomcat的配置文件server.xml中进行修改，我们可以找到server.xml配合文件中以下的地方

```
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" URIEncoding="UTF-8"/>
```
增加后的内容如下：

```
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"
           compression="on"
           compressionMinSize="2048"
           noCompressionUserAgents="gozilla, traviata"
           compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain,text/json,application/x-javascript,application/javascript,application/json"
           URIEncoding="UTF-8"/>	
```

注意：compressableMimeType这个属性在tomcat 7中要写上 application/x-javascript，否则不会对js压缩。

最后重启tomcat，使用chrome浏览器校验一下即可。