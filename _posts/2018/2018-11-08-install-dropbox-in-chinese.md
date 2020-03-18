---
layout: post
title: dropbox国内下载离线安装版
category: program
tags: [dropbox]
---

### 记录一下

每次dropbox让升级客户端，自动更新一直更新失败，如果可以直接下载安装包安装就好了。

现在，只需要在原下载的链接后加上“&full=1”即可。就像这样：

```

https://www.dropbox.com/downloading?src=index&full=1

```

### 在ubuntu下安装

在ubuntu下按照以上的操作是不可行的，按照以上方式操作下载的还是一个安装程序，安装程序还需要在线连接。

可以使用proxychains工具协助下载，具体操作见下面：


```
sudo apt install proxychains

//--- 使用编辑器打开下面的文件 --- //
sudo gedit /ect/proxychains.conf

// 将最后一行修改为  socks5 127.0.0.1 1080

proxychains dropbox start -i

```