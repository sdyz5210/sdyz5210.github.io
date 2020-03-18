---
layout: post  
title: 在Centos7 上安装Zabbix监控软件[1]
date: 2017-11-07 
tags: [zabbix,centos]  

--- 

### 背景
公司负责运维的同事小强离职了，接替的人暂时没有招聘到位，所以在这个空档了解了一下运维监控软件zabbix的安装及使用。本文记录一下自己动手安装的过程。

* 操作系统：Centos7
* zabbix版本：zabbix-2.4.3  公司已使用版本是这个，所以没有使用更高级版本

### 服务器端安装

#### 1、安装开发软件包

yum  -y groupinstall  "Development Tools"

#### 2、安装依赖包

yum -y install httpd php php-mysql php-common php-mbstring php-gd php-odbc php-pear curl curl-devel net-snmp net-snmp-devel perl-DBI php-xml ntpdate  php-bcmath wget

#### 3、安装并配置数据库

在centos7中，mysql不会自动安装，需要自动安装mariadb

```
 #安装mariadb数据库
 yum -y install mariadb*
 启动数据库服务
 systemctl start mariadb
 #设置开机启动
 #systemctl enable mariadb
 #设置数据库root账号密码
 mysql_secure_installation
```

#### 4、同步服务端的时间，保持所有服务器时间一致避免出现时间不同导致的不可用的监控数据

ntpdate pool.ntp.rog

#### 5、创建zabbix服务运行所需要的用户和组

groupadd  -g 201  zabbix  
useradd  -g zabbix  -u 201 -m zabbix

#### 6、创建zabbix所需的数据库

```
create database zabbix character set utf8;
grant all privileges on zabbix.* to zabbixuser@'%' identified by 'password';
flush privileges;
```

#### 7、下载zabbix

```
wget -c https://jaist.dl.sourceforge.net/project/zabbix/ZABBIX%20Latest%20Stable/2.4.3/
tar -zxvf zabbix-2.4.3.tar.gz
cd zabbix-2.4.3

```

#### 8、将zabbix数据库导入到mariadb

```
mysql -uzabbixuser -p zabbix < ~/zabbix-2.4.3/database/mysql/schema.sql
mysql -uzabbixuser -p zabbix < ~/zabbix-2.4.3/database/mysql/images.sql
mysql -uzabbixuser -p zabbix < ~/zabbix-2.4.3/database/mysql/data.sql
```

#### 9、编译安装zabbix

此处指定sysconfdir配置文件的路径就在/etc/zabbix/目录下了，如果不指定默认在/usr/local/etc下
```
./configure --sysconfdir=/etc/zabbix/ --enable-server --enable-agent --with-net-snmp --with-libcurl --with-mysql
make && make install
```
#### 10、设置开机自动启动

由于centos6、7在版本使用上有些差异，所以在配置开机启动时有两种方式，这里标记为centos6和centos7两种方式

* centos6

```
cp misc/init.d/tru64/zabbix_agentd /etc/init.d/
cp misc/init.d/tru64/zabbix_server /etc/init.d/
chmod +x /etc/init.d/zabbix_*
```
* centos7

```
[Unit]
Description=zabbix
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/usr/local/sbin/zabbix_server start ; /usr/local/sbin/zabbix_agentd start
ExecReload=/usr/local/sbin/zabbix_server reload ; /usr/local/sbin/zabbix_agentd reload
ExecStop=/usr/local/sbin/zabbix_server stop ; /usr/local/sbin/zabbix_agentd reload

[Install]
WantedBy=multi-user.target
```

#### 10、部署zabbix服务

```
mkdir /var/www/html/zabbix
cp -a frontends/php/* /var/www/html/zabbix/
chown -R  apache.apache /var/www/html/zabbix/
```

#### 11、配置php文件，适应zabbix安装所需的参数

```
vim /etc/php.ini
date.timezone = Asia/Shanghai
max_execution_time = 300
max_input_time = 300
post_max_size = 32M
```

#### 12、配置apache

```
 #暂未做特殊配置
vim /etc/httpd/conf/httpd.conf
ServerName 127.0.0.1:80
```

#### 13、zabbix server 配置

```
vim /etc/zabbix/zabbix_server.conf

DBHost=192.168.239.130
DBName= zabbix
DBUser=zabbixuser
DBPassword=zabbixpass
StartPollers=30                         #开启多线程数，一般不要超过30个
StartTrappers=20                        #trapper线程数
StartPingers=10                         #fping线程数
StartDiscoverers=120            
MaxHousekeeperDelete=5000       
CacheSize=1024M                         #用来保存监控数据的缓存数，根据监控主机的数量适当调整
StartDBSyncers=8                        #数据库同步时间
HistoryCacheSize=1024M          
TrendCacheSize=128M                     #总趋势缓存大小
HistoryTextCacheSize=512M
AlertScriptsPath=/etc/zabbix/alertscripts
LogSlowQueries=1000

```

#### 14、启动apache服务
接下来访问 http://IP/zabbix完成安装

### clinet 端安装

#### 1、安装开发环境

yum -y groupinstall "Development Tools"

yum –y install ntpdate

#### 2、同步时间

ntpdate pool.ntp.org

#### 3、创建zabbix用户和组
groupadd  -g 201 zabbix

useradd -g zabbix -u 201 -m zabbix

#### 4、安装

```
tar xf zabbix-2.4.3.tar.gz
tar -zxvf zabbix-2.4.3.tar.gz
cd zabbix-2.4.3
./configure --sysconfdir=/etc/zabbix --enable-agent
make && make install
```

#### 5、设置agent自动运行

* centos6

```
cp misc/init.d/tru64/zabbix_agentd /etc/init.d/
chmod +x /etc/init.d/zabbix_*

```

* centos7

```
[Unit]
Description=zabbix
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/usr/local/sbin/zabbix_agentd start
ExecReload=/usr/local/sbin/zabbix_agentd reload
ExecStop=/usr/local/sbin/zabbix_agentd reload

[Install]
WantedBy=multi-user.target
```

#### 6、配置agent

```
vim /etc/zabbix/zabbix_agentd.conf       #此处千万别写成了zabbix_agent.conf,否则配置了不生效
Server=192.168.239.130                   #填写Server的IP地址
ServerActive=192.168.239.130             #修改为Server的IP地址
Hostname=Centos-03                       #填写本机的HostName,注意Server端要能解析
UnsafeUserParameters=1                   #是否允许自定义的key,1为允许，0为不允许
Include= etc/zabbix/zabbix_agentd.conf.d/#自定义的agentd配置文件(key)可以在这里面写；

```