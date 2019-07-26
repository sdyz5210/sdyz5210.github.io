---
layout: post
title: 清理ubuntu系统
tags: [ubuntu18.04]

---

### 删除缓存

```
sudo apt-get autoclean 清理旧版本的软件缓存
sudo apt-get clean 清理所有软件缓存
sudo apt-get autoremove 删除系统不再使用的孤立软件

```

### 删除软件

```
ubuntu软件的删除一般用“ubuntu软件中心”或“新立得”就能搞定，但有时用命令似乎更快更好～～
sudo apt-get remove --purge 软件名
sudo apt-get autoremove 删除系统不再使用的孤立软件
sudo apt-get autoclean 清理旧版本的软件缓存
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P 清除残余的配置文件保证干净

```

### 清除孤立的包

```
sudo apt-get autoremove

 #使用deborphan来协助
sudo apt-get install deborphan

 # 列出孤立软件
 deborphan
 #清理孤立软件
deborphan | xargs sudo apt-get purge -y

```