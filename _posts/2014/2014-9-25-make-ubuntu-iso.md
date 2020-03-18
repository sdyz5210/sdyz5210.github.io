---
layout: post
title: 制作自己的ubuntu镜像iso
category: program
tags: [ubuntu,remastersys]  
---

#需求#
最近开发了一个BS的软件，软件主要依赖linux操作系统，并且需要perl语言的支持同时算法会依赖一些linux动态库及组件。如果仅仅是这些需求也就完了，但是客户需要把这套软件制作成单机版，方便一些没有网络的地方使用，同时方便报批国家标准。
程序是javaweb项目，即使我们在一个机器上部署也需要安装 jdk、tomcat、数据库等软件。开发人员去实施一点问题都没问，但是如果让客户去给毫无经验的人推广或者报批就麻烦了。同时程序中的特定算法必须在linux操作系统下面运行。所以。。。

#解决方案#
根据我们的程序，结合现实需求，我们开始打算打包tomcat、jdk、数据库等软件成为一个安装包，让客户直接在liunx系统下载安装即可。但是这里面又会涉及很多依赖包的问题，如果这些前置软件安装不好，即使打包出来的软件安装上也不可以使用，况且客户对linux系统不怎么熟悉。
解决方案：
1、打包安装包，我们确实使用shell命令执行软件的安装等过程，但是最终也实现，但是考虑到客户使用易用性，我们放弃了该方案
2、在安装好的linux（ubuntu14.04_x64 desktop）机器上安装好所有的软件，并且部署完成，设置好服务自动启动后。我们把设置好的整个系统制作成一个完成的系统镜像，此系统镜像已经完全包含了我们的软件的允许环境及软件，用户只需要在机器上安装上这个系统既可以正常使用。

#实施步骤#
通过google查找资料，我们决定使用remastersys工具来帮我们制作ubuntu镜像文件。首先我们在一台机器或者一个虚拟机中安装ubuntu操作系统，我们使用的版本是：ubuntu14.04_x64，安装过程不多说了。

1、首先通过源来安装软件，在终端使用`vim /etc/apt/sources.list`命令编辑该文件，在文件中增加源：

```
deb http://www.remastersys.com/ubuntu precise main
```

2、执行update操作

```
sudo apt-get update
```

3、执行安装

```
sudo apt-get install remastersys
```

4、检测安装是否成功

```
sudo remastersys
```

5、生成自己的镜像文件

```
sudo remastersys dist cdfs //这一步时间有点长，需耐心等待
sudo remastersys dist iso filename.iso //filename是你需要生成的镜像的名字
```

执行完成后，我们可以在/home/remastersys目录中找到iso文件，我们可以把iso文件刻录到光盘中保存或者分发。

#语法说明#

```
remastersys的语法: sudo remastersys backup|clean|dist [cdfs|iso] [filename.iso]
```

1)将你的系统备份制成一个live cd/dvd 
`sudo remastersys backup`
2) 将你的系统备份制成一个live cd/dvd其名叫 custom.iso 的ISO文件.
`sudo remastersys backup custom.iso`
3)清除由 remastersys产生的临时文件 
`sudo remastersys clean`
4) 产生一个可发布又包括你的个人资料的 livecd/dvd
`sudo remastersys dist www.2cto.com`
5) 产生一个只有档案系统的可发布的 livecd/dvd
`sudo remastersys dist cdfs`
6) 为了产生一个可发布又包括你的个人资料的 livecd/dvd 其名叫 custom.iso的ISO文件:
`sudo remastersys dist iso custom.iso`