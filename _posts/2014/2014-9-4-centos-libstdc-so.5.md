---
layout: post
title: CentOS缺少libstdc++.so.5问题 
category: program 
tags: [centos,question]  
---  

今天在centos系统上执行命令是出现错误，`error while loading shared libraries: libstdc++.so.5: cannot open shared object file: No such file or directory`.如下图所示：
![error](/images/centos1.jpg)

解决办法：
1、执行命令`yum whatprovides libstdc++.so.5`，然后会提示哪个安装包有这个库文件如下：

```
[root@cluster ~]# yum whatprovides libstdc++.so.5
compat-libstdc++-33-3.2.3-69.el6.i686 : Compatibility standard C++ libraries
Repo        : base
Matched from:
Other       : libstdc++.so.5



libstdc++5-3.3.6-8.mga3.x86_64 : GNU C++ library
Repo        : Rocks-6.1
Matched from:
Other       : libstdc++.so.5



compat-libstdc++-33-3.2.3-69.el6.i686 : Compatibility standard C++ libraries
Repo        : Rocks-6.1
Matched from:
Other       : libstdc++.so.5

```

2、然后执行：

```
yum install compat-libstdc++-33-3.2.3-69.el6.i686 
```

解决。
![ok](/images/centos2.png)
