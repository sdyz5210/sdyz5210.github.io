---
layout: post
title: Centos安装perl语言和perl模块
category: program   
tags: [perl]  
---


#写在前面的话#
默认安装的centos操作系统，基本上都已经默认安装了perl环境，所以如果仅仅是在一台机器上使用perl语言，我们基本上不需要重新安装perl，除非你需要特定的版本或者合同规定某个特定版本。本人遇到的问题不是特定版本的需求。  
真实需求：本人使用的SGE计算集群进行基因组的计算任务，计算节点都是通过管理节点安装的，整个集群顺利安装完成。针对软件的要求，需要安装相关的perl环境模块，因为计算节点有30多个，perl模块安装需要在这些节点上进行，工作量巨大，即使可以完成，后续维护和版本的升级管理也是很不方便，另外随着流程的增加perl依赖的模块也需要增加安装，这样就造成大量的重复性工作。  
解决方案：因为SGE集群的每个计算节点都可以访问到我们的一个共享存储，所以我们觉得把perl程序和module都安装存储上，统一调用存储上的perl即可。对于后期的运维升级也很方便。

#perl安装#

从perl官网下载perl的源码程序，当前我安装的版本为：5.14.2，下载地址：http://www.cpan.org/src/5.0/perl-5.14.2.tar.gz。  

存储上的安装目录为：/share/biosoft/perl/,我们把下载的perl的源程序放在/share/biosoft/中。解压缩下载的源文件，后进入解压缩后的文件夹。

```
//使用以下命令解压缩
tar -zxvf perl-5.14.2.tar.gz
cd perl-5.14.2
```
一般linux系统的源程序安装，都会存在一个“INSTALL”之类的安装说明，如果没有这个文件可以在readme找一些安装说明。安装说明中会详细描述软件安装的方式及执行的命令。读取安装说明可以帮我我们更好的安装。

接下来我们开始perl的编译安装。在安装之前可以需要安装一些基本的依赖，这些依赖如果已经安装可以不需要考虑,比如openssl-devel等，这里不细说，接下来正式安装perl。

```
//使用-Dprefix指定安装目录为/share/biosoft/perl,这个就是我们要安装的存储的位置
./Configure -des -Dprefix=/usr/local/perl/
make
make test
make install
```
使用以上命令既可以安装perl过程中如果需要其他依赖，需要提前安装后再执行上述步骤。

perl安装完之后，我们发现我们调用程序时，并不是使用我们安装的perl，使用的还是系统自带的程序，接下来我们需要进行perl版本的替换：

```
//备份旧的perl命令
mv /usr/bin/perl /usr/bin/perl.bak
//把我们新安装的perl的命令链接到/usr/bin/perl
ln -s /share/biosoft/perl/bin/perl /usr/bin/perl
```
经过上述的步骤，我们可以直接在命令行中访问我们自己安装的perl。

perl编译安装完成。


#perl模块安装#

perl模块的安装方式一般情况下使用cpan命令进行安装，但是接下来我们不使用这个命令安装，我们使用源安装module。

接下来的例子我们以安装perl的 SVG 模块为例子。

```
cd /share/biosoft/perlModule
wget -c http://search.cpan.org/CPAN/authors/id/S/SZ/SZABGAB/SVG-2.59.tar.gz
tar -zxvf SVG-2.59.tar.gz
cd SVG-2.59
perl Makefile.PL
make install 
```

执行以上命令即可安装完成。

#注意#
要想使每个节点都可以使用我们自己安装的perl，必须在每个节点上执行一次旧版本替换操作即可。