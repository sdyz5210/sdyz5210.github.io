---
layout: post
title: python安装PIL的问题
catetory: python
tags: [question]
---


最近在学习Tkinter的知识,这使用图片时发现不能正常显示，google一下，发现Tkinter默认支持gif格式，使用其他格式的文件需要安装PIL模块。不过在安装过程中出现了一下错误问题：

```
1 warning generated.
clang: warning: -framework Tcl: 'linker' input unused
clang: warning: -framework Tk: 'linker' input unused
In file included from _imagingtk.c:19:
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.10.sdk/usr/include/tk.h:78:11: fatal error:
      'X11/Xlib.h' file not found
#       include <X11/Xlib.h>
                ^
1 error generated.
error: Setup script exited with error: command 'cc' failed with exit status 1

```

解决方式：

```
brew install libtiff libjpeg webp little-cms2
```
执行完后再次执行安装，还是报同样地错误，经过查资料

```
Xcode-select --install
```

执行完上述命令再次执行即成功