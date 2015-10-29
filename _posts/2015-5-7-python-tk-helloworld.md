---
layout: post
title: Python TK编程1-helloword
catetory: program
tags: [python]
---  

最近在学习python，基本的语法看了一遍了，想了解一下gui编程方面的知识，所以对Tkinter进行一些了解，此博客记录自己的学习过程。

#我的第一个python gui程序

```
 #!/usr/bin/evn python
 # -*- coding: utf-8 -*-
 #filename:helloworld.py

from Tkinter import * 
root = Tk()
w = Label(root,text="Hello,World!")
w.pack()
root.mainloop()

```

通过 `python helloworld.py`进行运行。

#程序讲解

1、首先导入Tkinter模块，它包含我们需要适用的一切和Tkinter有关的函数

	`from Tkinter import * `

2、为了初始化Tkinter，我们需要创建一个root控件。他是一个普通的窗口，包含一个标题栏和其他有窗口管理器提供的装饰

	`root = Tk()`

3、接下来，我们创建一个label控件作为root窗口的子控件

	`w = Label(root,text="Hello,World!").pack()`

Label控件可以显示文本/位图/图像

4、事实上窗口不会出现，除非我们让Tkinter进入时间循环
	
	`root.mainloop()`
