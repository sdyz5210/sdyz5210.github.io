---
layout: post
title: python操作配置文件
category: program
tags: [python]
---

python读写配置文件还是比较方便得。

* 基本的读取配置文件
** read(filename) 直接读取ini文件内容  
** sections() 得到所有的section，并以列表的形式返回  
** options(section) 得到该section的所有option  
** items(section) 得到该section的所有键值对  
** get(section,option) 得到section中option的值，返回为string类型  
** getint(section,option) 得到section中option的值，返回为int类型，还有相应的getboolean()和getfloat() 函数。  

* 基本的写入配置文件
** add_section(section) 添加一个新的section  
** set( section, option, value) 对section中的option进行设置，需要调用write将内容写入配置文件。  

```

#!/usr/bin/evn python
# -*- coding: utf-8 -*-
# python version 2.7.6

import ConfigParser

#config文件，文件名为config.ini
'''
[user]
username = summer
password = summer
age = 20

[machine]
ip = 127.0.0.1
port = 8080
'''

conf = ConfigParser.ConfigParser()
conf.read('config.ini')

#获取指定的section下面的username参数
username = conf.get("user", "username")
print username
password = conf.get('user','password')
print password

#获取所有的section
sections = conf.sections()
print sections

#更新某个属性的值
port = conf.get('machine','port')
print type(port),port,port=='8080'
if port == '8080':
	#conf.set('machine','port',80)
	conf.set('machine','port','80')
print conf.get('machine','port')

#增加section
conf.add_section('website')
conf.set('website','baidu','http://www.baidu.com')

#获取所有的section
sections = conf.sections()
print sections
print conf.get('website','baidu')

```