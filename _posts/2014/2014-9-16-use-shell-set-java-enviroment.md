---
layout: post
title: Shell脚本中设置Java环境变量
category: program 
tags: [shell,java]
---

因为特定需求，需要在linux系统下面进行安装JDK，需要在linux中写成shell脚本，安装JDK并且设置环境变量。linux中JDK的安装比较简单，直接解压缩即可。但是接下来设置环境变量可是困难了本人不久，毕竟shell脚本没有接触过。

#检测环境变量#
以下的脚本可以协助我们进行环境变量的检测。

```
#!/bin/bash
if[-z $JAVA_HOME];then
    echo "no exist"
else
    echo "exist"
fi
```

在这段代码的基础上，进行环境变量的配置，我的思路主要是把配置环境变量的内容直接追加写入/etc/profile中
#配置java环境变量#
在控制台直接使用命令创建install.sh文件，打开文件`vim install.sh`

```
#!/bin/bash
#get current path
basePath=$PWD;
if [ -z $JAVA_HOME ];then
        echo "set java evniroment start"
        echo "#set java evn" >> /etc/profile
        echo "export JAVA_HOME=$basePath/jdk1.7.0_55/" >> /etc/profile
        echo "export PATH=\$JAVA_HOME/bin:\$PATH" >> /etc/profile
        echo "export CLASSPATH=.:\$JAVA_HOME/lib/dt.jar:\$JAVA_HOME/lib/tools.jar" >> /etc/profile
        source /etc/profile
else
        echo "exist"
fi
```

OK，接下来我们执行以下，首先对install.sh文件授予执行权限：`chmod 755 install.sh`。然后执行`./install.sh`
然后通过`java -version`检查是否安装成功。
结果出乎意料：<font color="red">设置不成功</font>。

#查找原因#
首先查看/etc/profile文件，发现里面的内容已经添加成功，所以我们可以肯定是脚本中`source /etc/profile`这个命令没有执行。那我们手动执行以下，看看是否可以。在控制台手动执行该命令后，然后再次检测是否安装成功，<font color="red">成功</font>。
很是疑惑这个问题，哪怎么执行这个脚本才可以一次性成功呢？google了一下，发现使用以下命令可以正确执行：

```
source install.sh
```

source命令的作用就是用来执行一个脚本，那么： source test.sh 同直接执行 ./test.sh 有什么不同呢？比如您在一个脚本里export $abc=111，假如您用./test.sh执行该脚本，执行完毕后，您运行echo $abc，发现没有值，假如您用source来执行，然后再echo ,就会发现abc=111。因为调用./test.sh来执行shell是在一个子shell里运行的，所以执行后，结果并没有反应到父shell里，但是source不同他就是在本shell中执行的，所以能够看到结果。











