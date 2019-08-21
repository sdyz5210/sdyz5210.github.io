---
layout: post
title: win10和ubuntu18.04 双系统时间不一致的问题
tags: [win10，ubuntu18.04]

---

### 问题

最近新买的电脑，安装了win10和ubuntu18.04双系统，发现win10下的时间和ubuntu18.04的时间不一致，大约相差8个小时。

### 解决方案

在ubuntu下进行解决

```
sudo timedatectl set-local-rtc 1

sudo apt-get install ntpdate
sudo ntpdate time.windows.com

 # 将时间更新至硬件
sudo hwclock --localtime --systohc

```