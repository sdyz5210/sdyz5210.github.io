---
layout: post
title: centos7 常用命令记录
tags: [centos7]

---

在安装centos7系统的过程中，因为该版本和之前centos6的使用命令有不少的差异，故记录一下centos7中的常用命令。

## 修改主机名

'''

#永久性的修改主机名称，重启后能保持修改后的。
hostnamectl set-hostname xxx

'''

## 创建RSA密钥

'''
# 执行一下命令，默认回车即可
ssh-keygen -t rsa

'''

## 修改虚拟机为静态IP地址

主要修改此目录下/etc/sysconfig/network-scripts/ifcfg-enp0s3的文件

'''

TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
IPADDR=192.168.1.142
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=202.102.152.3
DNS2=8.8.8.8
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=a854c79e-5edf-4543-bb7d-7c6c6b92d4ff
DEVICE=enp0s3
ONBOOT=yes
ZONE=public

'''

## 重启网卡命令

'''

service network restart

'''