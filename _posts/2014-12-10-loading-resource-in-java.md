---
layout: post
title: Java加载资源的方式
category: program  
tags: [java]  
---

#绝对路径#
我们在本机写一些测试方法时，经常会直接把加载文件的路径写死，即写成绝对路径进行处理，毕竟这样节省时间。这里的例子我们忽略文件是否存在等异常问题。

```
String fileName = "/Users/summer/Documents/workspaces/test.properties";
Properties p = new Properties();
InputStream in = new FileInputStream(fileName);
p.load(in);
System.out.println(p);
```  
通过上述方式我们可以在绝对路径下获取响应的配置文件。但是实际项目中如果加载资源文件，不可能写死路径，毕竟开发和生产环境还是有很大差别。

#相对路径#
如果资源文件在开发的项目中，我们可以通过相对路径来获取，这样生产环境下面部署的时候可以不用考虑配置文件路径的问题。
但是资源文件在不同的目录下面，获取的方式也有很大的差异。资源文件在classes目录下面或者和在某个class类的目录中。
##资源文件的在classes跟目录下##

资源文件在classes目录下，资源文件的加载需要通过ClassLoader来获取，不同的是在java程序中获取ClassLoader的方式也有不同的方式：

###方式一###

```
String fileName = "test.properties";
Properties p = new Properties();
InputStream in = Thread.currentThread().getContextClassLoader().getResourceAsStream(fileName);
p.load(in);
System.out.println(p);
```  

###方式二###

```
String fileName = "test.properties";
Properties p = new Properties();
InputStream in = ClassLoader.getSystemResourceAsStream(fileName);
p.load(in);
System.out.println(p);
```  

###方式三###

```
String fileName = "test.properties";
Properties p = new Properties();
//Demo类是加载资源使用的类，本人在这里写成Demo类
InputStream in =Demo.class.getClassLoader().getResourceAsStream(fileName);
p.load(in);
System.out.println(p);
```  
##资源文件放置类目录下面##
资源文件放在类目录下面，我们可以使用类进行加载，例如com.summer.Demo类，com.summer目录下面同样存放这test.properties配置文件，我们可以在Demo类中加载配置文件，如下：

```
String fileName = "test.properties";
Properties p = new Properties();
//Demo类是加载资源使用的类，本人在这里写成Demo类
InputStream in = Demo.class.getResourceAsStream(fileName);
p.load(in);
System.out.println(p);
```  