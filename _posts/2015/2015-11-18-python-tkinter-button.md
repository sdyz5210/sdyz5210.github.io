---
layout: post
title: python tkinter按钮传递参数
category: program
tags: [python,tkinter]
---

最近在使用python tkinter实现一个小程序，但是实现button点击事件时，无法传递参数，google了一下原来需要使用lamdba语法。tkinter要求由按钮（或者其它的插件）触发的控制器函数不能含有参数，目的就是为了以统一的方式去调用他们。

```
def save(id):
	pass

button = Button(root,text="test",command=lambda:save(id))

```

这样就可以完美调用了