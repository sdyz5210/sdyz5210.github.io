---
layout: post  
title: mac上安装perl的AES加密模块异常  
category: program  
tags: [perl]  

--- 

### 问题

准备熟悉一下perl下的AES加密的功能，但是在安装Crypt::OpenSSL::AES模块时，系统一直提示找不到 openssl/aes.h 文件，openssl重新安装和升级到最新版本一直都是这样的错误。

'''
AES.xs:5:10: fatal error: 'openssl/aes.h' file not found  
 #include <openssl/aes.h>  
         ^  
1 error generated.  
make: *** [AES.o] Error 1  
  TTAR/Crypt-OpenSSL-AES-0.02.tar.gz  
    /usr/bin/make -- NOT OK  
'YAML' not installed, will not store persistent state  
Running make test  
  Can't test without successful make  
Running make install  
  Make had returned bad status, install seems impossible  
'''

### 解决方案

本机openssl是通过brew进行安装的，默认路径为：/usr/local/Cellar/openssl/1.0.2l/include
直接拷贝该路径下的 openssl目录到 /usr/local/include/即可。