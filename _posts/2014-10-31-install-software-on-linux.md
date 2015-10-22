---
layout: post
title: Liunx环境变量及软件安装
category: program
tags: [centos,biosoft]
---

#写在前面的话#

真正开始接触linux操作系统是在工作后，接触的是centos操作系统，当时开发的系统需要部署在centos上，所以慢慢地对centos有所了解。为了更好的学习，就在自己笔记本电脑上安装了ubuntu操作系统，从最初的10.10一直使用到了14.04。这之间服务器也一直在使用centos操作系统，从5.5到了6.5.
虽然使用linux操作系统很长时间了，但是总感觉对linux操作系统不是很有感觉，总是有不明白的地方。特别是linux系统软件的安装，即使能够正确安装还是有些胆战心惊的担心操作是错误的。

#问题#

经常安装软件，但是在centos上很多软件的安装都是直接解压缩安装，软件的运行必须cd到相应的文件夹中才可以运行，比如我刚刚安装了samtools软件，但是安装过后只能在本目录中使用命令，如果在其他目录中使用必须使用绝对路径。

#解决方案#

如果可以在不同的目录中使用安装后的命令，我们可以把samtools命令路径配置到系统的环境变量中，即可。

#方案实施#

下面的方案实施我们以安装samtools为例，同时samtools的安装目录位置为：/usr/local/software/samtools-0.1.19/

##方案一##
通常情况下，我们配置环境变量经常对/etc/profile文件进行编辑操作，eg

```

vim /etc/profile

//在该文件中添加

export SAMTOOLS=/usr/local/software/samtools-0.1.19
export PATH=$PATH:$SAMTOOLS

//使之即刻生效
source /etc/profile

```

这个方案在我们进行编辑profile文件时，我们可以看到该文件上面的提示:

```

 # /etc/profile

 # System wide environment and startup programs, for login setup
 # Functions and aliases go in /etc/bashrc

 # It's NOT a good idea to change this file unless you know what you
 # are doing. It's much better to create a custom.sh shell script in
 # /etc/profile.d/ to make custom changes to your environment, as this
 # will prevent the need for merging in future updates.
 
```

系统并不建议修改此文件，所以我们可以采用第二方案。

##方案二##

首先我们在 /etc/profile.d/目录下面创建一个文件，比如我们以samtools为例。


```
cd /etc/profile.d/
touch samtools.sh
vim samtools.sh

//在samtools.sh文件中添加以下内容
export SAMTOOLS=/usr/local/software/samtools-0.1.19
export PATH=$PATH:$SAMTOOLS

//使之即刻生效
source /etc/profile.d/samtools.sh

```

这样我们就可以在其他目录使用samtools命令了。

完成。