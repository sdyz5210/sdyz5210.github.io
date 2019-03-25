---
layout: post
title: 在Centos7上配置主机名和IP的映射
tags: [centos]
---

### 修改主机名

在Centos7可以使用一下命令进行主机名修改

```

hostnamectl set-hostname xxx

```

### 配置主机名和IP的映射

修改主机名和IP的映射关系，需要在/etc/hosts增加对应关系:

```

vim /etc/hosts

#在里面添加
192.168.1.23  web

```