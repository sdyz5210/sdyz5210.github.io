---
layout: post
title: Pyramid安装与配置
category: program   
tags: [Pyramid]  
---

##前言
安装软件之前需要确保你已经安装了python，Mac系统自带了python，如果是其他系统可以考虑安装。

##安装Setuptools  

首先从https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py这个网址下载ez_setup.py，然后使用python进行安装，步骤如下：

```
wget -c https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py
sudo python ez_setup.py
```
如果不确定是否已经安装了setuptools，可以进入python命令行，输入命令`import setuptools`进行检测，如果报错，那证明setuptools没有安装。

##安装virtualenv
使用下列命令进行安装
`sudo easy_install virtualenv`  

##安装Pyramid
```
virtualenv --no-site-packages env
cd env
bin/easy_install pyramid
```
##创建项目

```
#创建一个alchemy的项目  
../bin/pcreate -s alchemy pythonweb
#如果是第一次运行需要初始化数据库
../bin/initialize_pythonweb_db development.ini
#运行需要三个步骤：测试、部署、运行
../bin/python setup.py develop
../bin/python setup.py test -q
#运行测试服务器
../bin/pserve development.ini --reload
```
运行pserve后就可以在浏览器中输入http://0.0.0.0:6543中查看web应用了