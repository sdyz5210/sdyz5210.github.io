---
layout: post  
title: centos6升级python  
category: program  
tags: [python]  

---

1、`yum install libjpeg-devel gcc gcc-c++  zlib-devel bzip2-devel openssl-devel xz-libs`

2、下载python最新版2.7.10解压缩后进行安装

```
	tar -zxvf Python-2.7.10.tgz
	cd Python-2.7.10/
	./configure --prefix=/usr/local
	make all
  	make install
    make clean
    make distclean

```

3、安装setuptools工具

```
	tar -zxvf setuptools-18.4.tar.gz
 	cd setuptools-18.4
	python setup.py  install
```

4、安装pip

```
	tar -zxvf pip-7.1.2.tar.gz
	cd pip-7.1.2
	python setup.py install
```

5、其他安装

```
	pip install pillow
	pip install reportlab
```