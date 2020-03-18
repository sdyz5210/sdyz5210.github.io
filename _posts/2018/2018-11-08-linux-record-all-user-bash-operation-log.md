---
layout: post  
title: linux上记录每个用户的操作日志
date: 2018-11-08
tags: [linux]  

--- 

### 问题

一般我们可以用history命令来查看用户的操作记录，但是这个命令不能记录是哪个用户登录操作的，也不能记录详细的操作时间，且不完整；所以误操作而造成重要的数据丢失，就很难查到是谁操作的。

### 解决方案

我们可以通过脚本来实现记录每个登录用户的操作命令；我们可以编辑 `/etc/profile` 文件增加一些配置信息即可实现。我们不推荐直接修改 `/etc/profile` 文件，可以在 `/etc/profile.d/` 创建相应的操作。比如创建：log.sh,具体操作如下：

```
#基本操作命令
cd /etc/profile.d/
touch log.sh
vim log.sh

```

在log.sh文件中添加一下内容：

```

history
USER=`whoami`
USER_IP=`who -u am i 2>/dev/null| awk '{print $NF}'|sed -e 's/[()]//g'`
if [ "$USER_IP" = "" ]; then
USER_IP=`hostname`
fi
if [ ! -d /var/log/history ]; then
mkdir /var/log/history
chmod 777 /var/log/history
fi
if [ ! -d /var/log/history/${LOGNAME} ]; then
mkdir /var/log/history/${LOGNAME}
chmod 300 /var/log/history/${LOGNAME}
fi
export HISTSIZE=4096
DT=`date +"%Y%m%d_%H:%M:%S"`
export HISTFILE="/var/log/history/${LOGNAME}/${USER}@${USER_IP}_$DT"
chmod 600 /var/log/history/${LOGNAME}/*history* 2>/dev/null

```

最后使用` source /etc/profile` 来生效配置。之后我们可以再这里看到存放的日志：/var/log/history 该目录下以每个用户为名创建一个文件夹，每次用户退出后都会产生以用户名、登录IP、时间的日志文件，以及用户本次的所有操作。