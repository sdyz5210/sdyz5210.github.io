---
layout: post  
title: perl程序安装模块  
category: program  
tags: [perl]  

---

#用cpan命令在线安装  

##安装依赖

```
yum -y install unzip links ftp make wget gpg
yum -y install cpan

```

##使用cpan命令安装,eg

`cpan -i Net::Telnet`