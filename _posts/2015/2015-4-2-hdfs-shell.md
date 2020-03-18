---
layout: post
title: HDFS中shell的常用命令
category: program
tags: [hdfs,hadoop]
---

#HDFS中shell命令语法格式

HDFS 是Hadoop分布式文件系统，那么对HDFS的操作，就是文件系统的基本操作，比如文件的创建、修改、删除、修改权限等，文件夹的创建、删除、重命名等。对HDFS 的操作命令类似于lLinux 的shell 对文件的操作，如ls、mkdir、rm 等。
我们可以再已经安装HDFS文件系统的操作系统上使用‘hadoop fs’ 看到基本命令，如下图： 

![hdfs1](../../images/hdfs1.png)  

#命令解释  

<table style="width: 100%; height: 100%;" border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td width="115">
<p>选项名称</p>
</td>
<td width="403">
<p>使用格式</p>
</td>
<td width="163">
<p>含义</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-ls </p>
</td>
<td valign="top" width="403">
<p>-ls &lt;路径&gt; </p>
</td>
<td valign="top" width="163">
<p>查看指定路径的当前目录结构</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-lsr </p>
</td>
<td valign="top" width="403">
<p>-lsr &lt;路径&gt; </p>
</td>
<td valign="top" width="163">
<p>递归查看指定路径的目录结构</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-du</p>
</td>
<td valign="top" width="403">
<p>-du &lt;路径&gt;</p>
</td>
<td valign="top" width="163">
<p>统计目录下各文件大小</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-dus</p>
</td>
<td valign="top" width="403">
<p>-dus &lt;路径&gt; </p>
</td>
<td valign="top" width="163">
<p>汇总统计目录下文件(夹)大小</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-count </p>
</td>
<td valign="top" width="403">
<p>-count [-q] &lt;路径&gt; </p>
</td>
<td valign="top" width="163">
<p>统计文件(夹)数量</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-mv</p>
</td>
<td valign="top" width="403">
<p>-mv &lt;源路径&gt; &lt;目的路径&gt; </p>
</td>
<td valign="top" width="163">
<p>移动</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-cp</p>
</td>
<td valign="top" width="403">
<p>-cp &lt;源路径&gt; &lt;目的路径&gt; </p>
</td>
<td valign="top" width="163">
<p>复制</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-rm</p>
</td>
<td valign="top" width="403">
<p>-rm [-skipTrash] &lt;路径&gt; </p>
</td>
<td valign="top" width="163">
<p>删除文件/空白文件夹</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-rmr</p>
</td>
<td valign="top" width="403">
<p>-rmr [-skipTrash] &lt;路径&gt;</p>
</td>
<td valign="top" width="163">
<p>递归删除</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-put </p>
</td>
<td valign="top" width="403">
<p>-put &lt;多个linux上的文件&gt; &lt;hdfs 路径&gt; </p>
</td>
<td valign="top" width="163">
<p>上传文件</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-copyFromLocal </p>
</td>
<td valign="top" width="403">
<p>-copyFromLocal &lt;多个linux 上的文件&gt; &lt;hdfs 路径&gt;</p>
</td>
<td valign="top" width="163">
<p>从本地复制</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-moveFromLocal </p>
</td>
<td valign="top" width="403">
<p>-moveFromLocal &lt;多个linux 上的文件&gt; &lt;hdfs 路径&gt;</p>
</td>
<td valign="top" width="163">
<p>从本地移动</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-getmerge </p>
</td>
<td valign="top" width="403">
<p>-getmerge &lt;源路径&gt; &lt;linux 路径&gt; </p>
</td>
<td valign="top" width="163">
<p>合并到本地</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-cat</p>
</td>
<td valign="top" width="403">
<p>-cat &lt;hdfs 路径&gt; </p>
</td>
<td valign="top" width="163">
<p>查看文件内容</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-text </p>
</td>
<td valign="top" width="403">
<p>-text &lt;hdfs 路径&gt; </p>
</td>
<td valign="top" width="163">
<p>查看文件内容</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-copyToLocal </p>
</td>
<td valign="top" width="403">
<p>-copyToLocal [-ignoreCrc] [-crc] [hdfs 源路径] [linux 目的路径]</p>
</td>
<td valign="top" width="163">
<p>复制到本地</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-moveToLocal</p>
</td>
<td valign="top" width="403">
<p>-moveToLocal [-crc] &lt;hdfs 源路径&gt; &lt;linux目的路径&gt;</p>
</td>
<td valign="top" width="163">
<p>移动到本地</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-setrep</p>
</td>
<td valign="top" width="403">
<p>-setrep [-R] [-w] &lt;副本数&gt; &lt;路径&gt; </p>
</td>
<td valign="top" width="163">
<p>修改副本数量</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-mkdir</p>
</td>
<td valign="top" width="403">
<p>-mkdir &lt;hdfs 路径&gt; </p>
</td>
<td valign="top" width="163">
<p>创建空白文件夹</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-touchz</p>
</td>
<td valign="top" width="403">
<p>-touchz &lt;文件路径&gt;</p>
</td>
<td valign="top" width="163">
<p>创建空白文件</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-stat</p>
</td>
<td valign="top" width="403">
<p>-stat [format] &lt;路径&gt; </p>
</td>
<td valign="top" width="163">
<p>显示文件统计信息</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-tail </p>
</td>
<td valign="top" width="403">
<p>-tail [-f] &lt;文件&gt; </p>
</td>
<td valign="top" width="163">
<p>查看文件尾部信息</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-chmod</p>
</td>
<td valign="top" width="403">
<p>-chmod [-R] &lt;权限模式&gt; [路径] </p>
</td>
<td valign="top" width="163">
<p>修改权限</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-chown </p>
</td>
<td valign="top" width="403">
<p>-chown [-R] [属主][:[属组]]路径</p>
</td>
<td valign="top" width="163">
<p>修改属主</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-chgrp </p>
</td>
<td valign="top" width="403">
<p>-chgrp [-R] 属组名称 路径</p>
</td>
<td valign="top" width="163">
<p>修改属组</p>
</td>
</tr>
<tr>
<td valign="top" width="115">
<p>-help </p>
</td>
<td valign="top" width="403">
<p>-help -help [命令选项] </p>
</td>
<td valign="top" width="163">
<p>帮助</p>
</td>
</tr>
</tbody>
</table>