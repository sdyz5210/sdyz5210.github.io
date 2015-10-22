---
layout: post
title: Eclipse3.7配置Jboss7
category: program
tags: [jboss]
---

#简介#
最近公司框架确定使用jboss作为生产环境的应用服务器，所以在开发过程中同样使用jboss进行开发，所以今天在eclipse中进行了jboss的配置。

现在jboss的版本为7.1.1，但是eclipse中支持的版本为5.0，所以我们需要对eclipse进行操作使之可以支持jboss7.1.1。

#现状#
如下图，默认eclipse中仅支持到5.0版本。
![eclipse](/images/eclipse1.png)
![eclipse](/images/eclipse2.png)

#工具下载#
若要eclipse支持到更高版本的jboss，需要安装jboss tools，目前该版本为：3.3.2。工具下载地址[jboss tools](http://tools.jboss.org/downloads/jbosstools/indigo/3.3.2.Final.html),下载下来的文件为：jbosstools-3.3.2.Final.aggregate-Update-2013-01-18_18-03-25-H229.zip

#安装#
为eclipse安装插件有很多方式：
*直接通过eclipse在线安装，安装之后eclipse和插件基本上耦合在一起，这个方式本人不是很喜欢。
*下载离线安装包，同样通过eclipse中的install new software进行离线安装
*下载离线安装包，使用link方式进行安装
本人安装eclipse插件基本上喜欢第三种方式，这种方式可以避免在线安装等待很久也可能不成功的问题，同时安装的插件和原来eclipse分开松耦合。

使用第三种安装方式操作如下：
1、把下载的tools安装包拷贝到一个目录下面，我的放在/home/summer/work/plugin中了
2、解压缩文件，修改文件夹名称为：jbosstools（因为解压缩后文件夹名字太长，修改短些方便操作）
3、在eclipse安装目录下面dropins文件夹中创建一个文本文件，文件名为jbosstools.link.注意一定要使用link后缀。
4、编辑jbosstools.link文件，输入`path=\home\summer\work\plugin\jbosstools` 保存即可。
5、重启eclipse即可，再次检查我们是否可以安装成功，如下图：
![eclipse](/images/eclipse3.png)
![eclipse](/images/eclipse4.png)

OK,插件安装成功。