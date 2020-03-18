---
layout: post
title: Python用PyInstaller打包笔记
category: program
tags: [python]
---

使用 python 编写的程序，打算发布到windows平台上，希望windows上的用户需要安装python的执行环境即可执行，所以选择使用pyinstaller进行打包，发布成exe执行格式。

* 准备工作

** 安装pywin32

到http://sourceforge.net/projects/pywin32/下载 PyWin32，如果想发布到32位和64为操作系统上，这里可以分别选择pywin32-219.win32-py2.7 和 pywin32-219.win-amd64-py2.7

** 安装pyinstaller

到http://www.pyinstaller.org/下载PyInstaller并解压缩。本文使用PyInstaller 2.1。

** 下载upx（可选）

到http://upx.sourceforge.net/下载upx，解压后把upx放在PyInstaller目录下，upx的作用是给生成的exe加壳，减小体积。

* 使用方法

首先进入到pyinstaller的目录下面。我的python源程序存放位置在D:\learn\client\下。使用下述命令进行打包程序。

```
python pyinstaller.py -F -w --icon=D:\learn\client\images\favicon.ico D:\learn\client\app.py

# -F 生成一个可执行文件
# -w windows下启动隐藏dos命令行
# --icon 制定程序的logo
# 最后的参数是制定程序的入口

```

* 注意

** 打包过程中对于logo的处理需要软件 请使用 png2ico 进行处理。程序下载地址：http://www.winterdrache.de/freeware/png2ico/