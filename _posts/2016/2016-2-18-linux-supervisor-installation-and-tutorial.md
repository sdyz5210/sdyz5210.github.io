---
layout: post
title: Supervisor安装使用教程
category: program
tags: [python,linux,supervisor]
---

Supervisor (http://supervisord.org) 是一个用 Python 写的进程管理工具，可以很方便的用来启动、重启、关闭进程（不仅仅是 Python 进程）。除了对单个进程的控制，还可以同时启动、关闭多个进程，比如很不幸的服务器出问题导致所有应用程序都被杀死，此时可以用 supervisor 同时启动所有应用程序而不是一个一个地敲命令启动。

##安装##

因为Supervisor该组件使用python开发的，所以它的安装我们也可以pip进行安装。Supervisor 可以运行在 Linux、Mac OS X 上。

```
pip install supervisor
#如果你使用的是 ubunut 系统，还可以使用下面的命令安装
apt-get install supervisor
```

##supervisord 配置##

Supervisor 相当强大，提供了很丰富的功能，不过我们可能只需要用到其中一小部分。安装完成之后，可以编写配置文件，来满足自己的需求。首先我们创建配置文件。

```
#默认生成配置文件在下面路径
echo_supervisord_conf > /etc/supervisord.conf
#同样可以指定配置文件的存放目录
echo_supervisord_conf > /path/to/supervisord.conf
```

然后编辑该配置文件.配置文件supervisord.conf是一个ini文件，可以对http_server、supervisord、supervisorctl和program进行配置。不过默认生成的文件已经对大部分进行配置，如果简单使用，只需要配置program的部分就可以了。配置文件必须要有一个program配置项，这样supervisord才知道哪个program需要被管理和监控。

```
[program:client]
command=python /home/summer/app.py
startsecs=0
stopwaitsecs=0
autostart=true
autorestart=true
stdout_logfile=/home/summer/log/app.log
stderr_logfile=/home/summer/log/app.err

```

##启动##

```
supervisord     #默认使用/etc/supervisord.conf的配置文件
supervisord -c /path/to/supervisord.conf

```

##通过supervisordctl管理进程##

```
[root@study ~]# supervisorctl
client                           RUNNING   pid 4568, uptime 0:00:01
supervisor> status
client                           RUNNING   pid 4568, uptime 0:00:04
supervisor> stop client
client: stopped
supervisor> status
client                           STOPPED   Feb 18 11:15 AM
supervisor> start all
client: started
supervisor> status
client                           RUNNING   pid 4589, uptime 0:00:03
supervisor>

```

##其他##

除了 supervisorctl 之外，还可以配置 supervisrod 启动 web 管理界面，这个 web 后台使用 Basic Auth 的方式进行身份认证。
除了单个进程的控制，还可以配置 group，进行分组管理。
经常查看日志文件，包括 supervisord 的日志和各个 pragram 的日志文件，程序 crash 或抛出异常的信息一半会输出到 stderr，可以查看相应的日志文件来查找问题。
Supervisor 有很丰富的功能，还有其他很多项配置，可以在官方文档获取更多信息：http://supervisord.org/index.html