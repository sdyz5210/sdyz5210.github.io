---
layout: post
title: 在Centos7上安装NIS
tags: [centos,nis]
---

### 前言

NIS（ NetworkInformation Service）提供了一个网络黄页的功能，当用户登录系统时，Linux系统会到NIS主机上去寻找用户使用的帐号密码信息加以比对，以提供用户登录检验。同时NIS服务器还可以提供其他多种信息。 

在一个大型的网域中，如果有多部Linux主机，而且需要每台主机都设定相同的帐号与密码时，是十分麻烦的。此时，如果能够有一台NIS主控制服务器（master server）来管理该网域中所有主机的帐号密码，当其他的主机有用户登录的需求时，才到这台服务器上请求相关的帐号密码等使用者资料，这样一来，如果想要增加、修改、删除用户的资料，只需要到这台服务器上面处理即可，这样就能够大大降低重复设定用户帐号密码的步骤，便于管理。NIS（Network Information Services） Server就可以实现这样的功能。

### 安装环境说明

```
[root@master ~]# cat /etc/redhat-release
CentOS Linux release 7.6.1810 (Core)

```

关闭防火墙和selinux。

```
vim /etc/selinux/config
#设置
SELINUX=disabled

#关闭防火墙及自动启动

systemctl stop firewalld
systemctl disable firewalld

#重启使生效

reboot

```

### Server端安装

1、先按照基本的组件，使用一下命令即可。

``` 
yum -y install ypserv ypbind yp-tool

```

2、设置域

编辑/etc/sysconfig/network文件，在里面增加一下内容：

```

NISDOMAIN=celloud

```

3、启动服务，并配置开机自动启动

```

systemctl start ypserv
systemctl start rpcbind
systemctl start yppasswdd

systemctl enable ypserv
systemctl enable rpcbind
systemctl enable yppasswdd.

```

4、创建一个账号并设置密码

```
useradd celloud
passwd celloud

```

5、创建用户信息数据库

```

/usr/lib64/yp/ypinit -m
# 新增账户时，需要更新数据库  
make -C /var/yp

```

### Client端安装

Client端安装同样需要关掉防火墙和selinux设置。

1、安装

``` 
yum -y install ypbind yp-tool

```

2、设置NIS域名

```
vim /etc/sysconfig/network

#增加一下内容
NISDOMAIN=celloud

```

3、配置账户信息的读取顺序

```
vim /etc/nsswitch.conf

passwd:     nis files sss
shadow:     nis files sss
group:      nis files sss
#initgroups: files sss

#hosts:     db files nisplus nis dns
hosts:      nis files dns myhostname

```

4、配置/etc/yp.conf

```
vim /etc/yp.conf

#增加一下内容
domain celloud server master

```

5、设置账号登入认证机制

```
vim /etc/sysconfig/authconfig

#修改一下参数的信息
USENIS=yes

```

6、设置PAM授权

```
vim /etc/pam.d/system-auth

#在最后增加一下内容
session     optional      pam_mkhomedir.so skel=/etc/skel umask=077

```

7、启动和设置开机启动

```
systemctl start rpcbind
systemctl start ypbind

systemctl enable rpcbind
systemctl enable ypbind

```

### Clinet端测试

1、yptest测序

```
Test 1: domainname
Configured domainname is "celloud"

Test 2: ypbind
Used NIS server: master

Test 3: yp_match
WARNING: No such key in map (Map passwd.byname, key nobody)

Test 4: yp_first
celloud celloud:$6$K3Q1vTSj$TLE69uM/QMVYCzXB:1000:1000::/home/celloud:/bin/bash

Test 5: yp_next

Test 6: yp_master
master

Test 7: yp_order
1553604033

Test 8: yp_maplist
mail.aliases
protocols.byname
protocols.bynumber
netid.byname
services.byservicename
services.byname
rpc.bynumber
rpc.byname
hosts.byaddr
hosts.byname
group.bygid
group.byname
passwd.byuid
passwd.byname
ypservers

Test 9: yp_all
celloud celloud:$6$K3Q1vTSj$TLE69uM/QMVYCzXB:1000:1000::/home/celloud:/bin/bash
1 tests failed

```

2、ypwhich

ypwhich 查看资料库映射数据

```
[root@node2 ~]# ypwhich
master

[root@node2 ~]# ypwhich -x
Use "ethers"	for map "ethers.byname"
Use "aliases"	for map "mail.aliases"
Use "services"	for map "services.byname"
Use "protocols"	for map "protocols.bynumber"
Use "hosts"	for map "hosts.byname"
Use "networks"	for map "networks.byaddr"
Use "group"	for map "group.byname"
Use "passwd"	for map "passwd.byname"

```

3、ypcat

可以利用ypcat读取数据库内容，具体使用方案参见帮助信息即可。

### 参考资料

* [Configure NIS](https://www.server-world.info/en/note?os=Fedora_20&p=nis&f=1)  
* [NIS - 深入了解如何搭建NIS环境](https://www.ctolib.com/topics-79802.html)  