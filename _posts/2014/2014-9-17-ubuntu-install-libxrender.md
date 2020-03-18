---
layout: post
title: Ubuntu安装libXrender.so.1动态库
category: program
tags: [ubuntu]
---

系统刚刚部署到阿里云的ubuntu-server操作系统上面，一直运行不来结果，但是也没有发现出错信息，经过多次查询发现缺少libXrender.so.1库。
安装libXrender.so.1库。
```
sudo apt-get install libxrender-dev 
sudo apt-get install libxtst-dev 
```