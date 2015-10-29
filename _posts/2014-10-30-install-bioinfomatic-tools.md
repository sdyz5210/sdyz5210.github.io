---
layout: post
title: 生物信息软件的安装[1]
category: bio
tags: [centos,biosoft]
---

#写在前面的话
本文主要记录常用的生物信息分析软件在centos操作系统上面的安装，生物信息分析的工作大部分和linux操作系统分不开，使用的很多软件也都需要在centos或其他操作系统上进行安装，本文以centos为基础进行。
#软件安装列表
1、samtools
2、R
3、Blast
4、Bwa
5、Bowie
#Samtools
本文采用的samtools安装版本为：samtools-0.1.19.tar.bz2，如果有特殊要求请再另行下载符合需要的版本。

```
//下载samtools
wget -c http://sourceforge.net/projects/samtools/files/samtools/0.1.19/samtools-0.1.19.tar.bz2/download
//安装依赖--需要根据具体情况确定，我在这里预安装zlib
yum install zlib zlib-devel
//解压缩
tar -jxvf samtools-0.1.19.tar.bz2
//进去samtools-0.1.19
cd samtools-0.1.19
//安装
make

```

安装完成，我们进入samtools目录，然后使用`./samtools`可以看到samtools的帮助参数，说明我们安装成功。

注意：如果用户在其他的操作系统上面安装，比如mac下面，可以参考《Mac上安装samtools》。
如果在ubuntu安装samtools，请注意。在make之前可能需要安装
```
sudo apt-get install zlib1g-dev
sudo apt-get install libncurses5-dev libncursesw5-dev
```
本人在ubuntu14.04上遇到过这个问题。

#R
R软件我们使用的版本为：R-3.1.1.tar.gz。
在安装R之前，需要提前安装一些组件，否则安装或者编译不成功：

```
 #gcc-gfortan:解决configure:error:No F77 compiler found的错误
 #gcc gcc-c++:解决configure:error:C++ preprocessor "/lib/cpp" fails sanity check
 #readline-devel:解决--with-readline=yes(default)and headers/libs are not available
 #libXt-devel:解决configure:error:--with-x=yes(default)and X11 headers/libs are not available
 
yum install gcc-gfortran gcc gcc-c++ readline-devel libXt-devel

```

```
//软件下载
wget -c wget -c http://cran.r-project.org/src/base/R-3/R-3.1.1.tar.gz
//解压缩软件
tar -zxvf R-3.1.1.tar.gz 
cd R-3.1.1
//安装
./configure 
make
make install

```
R软件安装成功后，可以在命令行中使用R命令，因为安装过程中已经把R命令加入到了系统环境变量中，所以R命令在所有目录中都可以直接使用。

#Blast
Blast软件我们使用的版本为：blast-2.2.26-x64-linux.tar.gz.
官方下载地址：http://ftp.ncbi.nlm.nih.gov/blast/executables/release/2.2.26，如果有特定版本的要求，可以在这里选择下载相应的版本。

```
//下载
wget -c http://ftp.ncbi.nlm.nih.gov/blast/executables/release/2.2.26/blast-2.2.26-x64-linux.tar.gz
//解压缩
tar -zxvf blast-2.2.26-x64-linux.tar.gz

```

该软件直接解压缩后可以使用，进入文件夹的bin目录我们可以看到可以使用的blast的命令。如果想在系统其他目录中使用该命令，可以直接使用绝对目录或者把命令直接加入的系统环境变量中。

#Bwa
本软件我们使用的版本为：bwa-0.7.10.tar.bz2

```
//下载
wget -c http://sourceforge.net/projects/bio-bwa/files/latest/download?source=files
//解压缩
tar -jxvf bwa-0.7.10.tar.bz2 
//进入目录并安装
cd bwa-0.7.10
make

```
安装完成后，直接使用命令`./bwa`即可

#Bowie

本软件我们使用的版本为：bowtie2-2.2.4-linux-x86_64.zip。

```
//下载
wget -c http://ftp.jaist.ac.jp/pub/sourceforge/b/bo/bowtie-bio/bowtie2/2.2.4/bowtie2-2.2.4-linux-x86_64.zip。
//解压缩
unzip bowtie2-2.2.4-linux-x86_64.zip
//进入解压缩后的文件夹
cd bowtie2-2.2.4

```

该软件解压缩后即可正常使用。进入目录后，直接使用`./bowtie2`命令

#结束#

为了方便使用我们安装的软件，我们可以把这些软件的执行命令添加到PATH环境变量中。添加环境变量的操作我们可以参考另外的一篇博文。