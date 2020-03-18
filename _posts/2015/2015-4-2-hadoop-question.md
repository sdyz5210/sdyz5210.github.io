---
layout: post
title: Hadoop开发过程中遇到的问题(1)
category: program
tags: [hadoop]
---

#前言
记录本人在从零摸索hadoop过程中遇到的问题或者错误，以备后续查看或使用。

#问题
####问题1：Permission denied: user=single, access=WRITE
<strong>问题描述</strong>
在Eclipse提交hadoop程序出错：Permission denied: user=***, access=WRITE, inode="staging":root:supergroup:rwxr-xr-x。

<strong>解决方法</strong>
到服务器上修改hadoop的配置文件：conf/hdfs-core.xml, 找到 dfs.permissions 的配置项 , 将value值改为 false

```
<property>
<name>dfs.permissions</name>
<value>false</value>
<description>
If "true", enable permission checking in HDFS.
If "false", permission checking is turned off,
but all other behavior is unchanged.
Switching from one parameter value to the other does not change the mode,
owner or group of files or directories.
</description>
</property>
```
修改完要重启下hadoop才能生效

