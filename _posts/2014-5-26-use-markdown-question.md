---
layout: post
title: MarkDown使用中的问题
category: program 
tags: [question]
---

#1、code的使用问题
为了让想想展示的内容使用code或者pre格式，在使用md格式时，最好在展示的内容前和后使用空格进行分割。markdown中使用code或者pre的格式分别是：只要简单地缩进 4 个空格或是 1 个制表符就可以
#2、code中插入连接的问题
按照以往的习惯，在md文件code区域编辑link，使用的连接markdown代码如下：

    [download](http://activemq.apache.org/download.html)
结果显示内容如下图
![图片未正常显示](/images/md1.png)

所以如果想在pre和code格式中使用超链接，怎么设置呢？
在markdown中，使用4个空格或者一个tab制表符形成的pre和code格式，之间的内容会放到双引号作为一个文本，所以里面的link不会运行点击。如果想要code中的链接可以点击，需要使用HTML的方式，代码如下：


```
<pre><code>
<a href="http://activemq.apache.org/download.html">download</a>
</code></pre>
```

效果如下所示：
<pre><code>
<a href="http://activemq.apache.org/download.html">download</a>
</code></pre>