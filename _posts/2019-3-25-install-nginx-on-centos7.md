---
layout: post
title: 在centos7上安装nginx
category: program
tags: [nginx,centos]
---

### 手动安装nginx

```

#安装依赖包
yum -y install pcre-devel openssl openssl-devel

#下载nginx的安装包，目前的稳定版本为1.14.2 
wget -c https://nginx.org/download/nginx-1.14.2.tar.gz

# 解压缩
tar -zxvf nginx-1.14.2.tar.gz

# 进入到目录
cd nginx-1.14.2 

./configure
make && make install

# 启动命令
/usr/local/nginx/sbin/nginx

# 重启
/usr/local/nginx/sbin/nginx -s reload

# 停止
/usr/local/nginx/sbin/nginx -s stop

```

### 本地测试是否正常启动

使用 ` curl http://192.168.1.150 ` 命令来确定是否启动成功。

```

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

```

看到以上内容，变为启动成功。

### 设置远程访问

centos7的默认使用firewall-cmd 来管理防火墙。需要开启 80 端口才可以进行远程访问。

```
# 查看防火墙的状态
systemctl status firewalld

# 关闭防火墙
systemctl stop firewalld

# 关闭防火墙开机自动启动
systemctl disable firewalld

# 查看端口是否开启,如果显示no为关闭状态
firewall-cmd --query-port=80/tcp

# 永久开启端口
firewall-cmd --zone=public --add-port=80/tcp --permanent

# 重启防火墙使之生效
firewall-cmd --reload

```
