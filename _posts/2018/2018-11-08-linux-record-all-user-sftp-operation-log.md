---
layout: post  
title: linux上记录每个用户的的sftp操作日志
date: 2018-11-08
tags: [linux,sftp]

--- 

### 1、修改SSH配置

```
vim /etc/ssh/sshd_config
#修改下面一行，在`Subsystem       sftp    /usr/lib64/ssh/sftp-server` 后面增加一下内容 
Subsystem       sftp    /usr/lib64/ssh/sftp-server -l INFO -f AUTH

```

### 2、修改rsyslog

```
vim /etc/rsyslog.conf
# 增加一行
auth,authpriv.*                                         /var/log/sftp.log

```

### 3、修重启生效

```
service sshd restart
service rsyslog restart

```
最终可以在`/var/log/sftp.log`里面查看具体的操作日志
