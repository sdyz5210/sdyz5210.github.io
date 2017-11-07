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

	#保存老版本并创建新版本链接
	mv /usr/bin/python /usr/bin/python2.6.6
	ln -s /usr/local/bin/python2.7 /usr/bin/python

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

6、修改yum的配置

此时更新换python后，yum应该是无法使用的，因为yum依赖于python2.6版本，现在的python版本已经是2.7了，修改yum的配置。

```
vim /usr/bin/yum
#!/usr/bin/python  -> #!/usr/bin/python2.6

```