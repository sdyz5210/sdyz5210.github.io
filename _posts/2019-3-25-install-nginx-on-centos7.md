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

### 配置 ssl 证书

使用 Let's Encrypt 来生成SSL证书，一般生成的证书存放的位置为：/etc/letsencrypt/live/xxxx.com/
Let's Encrypt 是由非盈利性互联网安全研究小组（ISRG）提供的免费的自动化和开放的证书颁发机构。目前各浏览器支持度比较好，可信度较高。

把下面这部分的代码注释去掉，然后配置相应的ssl证书

```

	server {
        listen       443 ssl;
        server_name  www.genecode.cn;

        ssl_certificate      /etc/letsencrypt/live/xxxx.com/fullchain.pem;
        ssl_certificate_key  /etc/letsencrypt/live/xxxx.com/privkey.pem;

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        location / {
            root   html;
            index  index.html index.htm;
        }
    }

```

同时可以强制http访问的链接转向https访问

```

	listen       80;
    server_name  www.genecode.cn;
	# 增加一下一行内容
	rewrite ^(.*) https://$server_name$1 permanent;

```

### nginx--代理

1、正向代理是指位于客户端和原始服务器之间的服务器，为了从原始服务器获取内容，客户端向代理服务器发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获取的内容返回客户端，客户端才能使用正向代理。常见例子：~翻~墙~软件

正向代理，架设在客户机和目标主机之间，代理客户端，服务器端不知道实际发送请求的客户端；

2、反向代理方式是指以代理服务器来接受internet上的链接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

反向代理，架设在服务器端，代理服务器端，客户端不知道实际提供服务的服务端；

### nginx--负载均衡

* 轮询(默认) ：每个请求按照时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除

* 指定权重：指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况

* IP 绑定 ip_hash:每个请求按照访问IP的hash结果分配，这样每个访问固定访问一个后端服务器，可以解决session的问题