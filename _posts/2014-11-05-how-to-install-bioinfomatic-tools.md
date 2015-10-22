---
layout: post
title: 生物信息软件安装[2]
category: bio
tags: [centos,biosoft]
---

在上一篇《生物信息软件安装[1]》中我们已经介绍了R语言的安装，不过我们在使用R的过程中，经常会遇到使用R的其他模块，比如需要使用ggplot2包，所以我们就需要在R安装的基础上安装ggplot2包。

安装完成R之后，进入R的控制台,按照以下命令进行操作即可：

```
[root@celloud ~]# R

R version 3.1.2 (2014-10-31) -- "Pumpkin Helmet"
Copyright (C) 2014 The R Foundation for Statistical Computing
Platform: x86_64-unknown-linux-gnu (64-bit)

R是自由软件，不带任何担保。
在某些条件下你可以将其自由散布。
用'license()'或'licence()'来看散布的详细条件。

R是个合作计划，有许多人为之做出了贡献.
用'contributors()'来看合作者的详细情况
用'citation()'会告诉你如何在出版物中正确地引用R或R程序包。

用'demo()'来看一些示范程序，用'help()'来阅读在线帮助文件，或
用'help.start()'通过HTML浏览器来看帮助文件。
用'q()'退出R.

> install.packages("ggplot2");
--- 在此連線階段时请选用CRAN的鏡子 ---
CRAN mirror 

  1: 0-Cloud                        2: Argentina (La Plata)       
  3: Argentina (Mendoza)            4: Australia (Canberra)       
  5: Australia (Melbourne)          6: Austria                    
  7: Belgium                        8: Brazil (BA)                
  9: Brazil (PR)                   10: Brazil (RJ)                
 11: Brazil (SP 1)                 12: Brazil (SP 2)              
 13: Canada (BC)                   14: Canada (NS)                
 15: Canada (ON)                   16: Canada (QC 1)              
 17: Canada (QC 2)                 18: Chile                      
 19: China (Beijing 1)             20: China (Beijing 2)          
 21: China (Hefei)                 22: China (Xiamen)             
 23: Colombia (Bogota)             24: Colombia (Cali)            
 25: Czech Republic                26: Denmark                    
 27: Ecuador                       28: El Salvador                
 29: Estonia                       30: France (Lyon 1)            
 31: France (Lyon 2)               32: France (Montpellier)       
 33: France (Paris 1)              34: France (Paris 2)           
 35: France (Strasbourg)           36: Germany (Berlin)           
 37: Germany (Bonn)                38: Germany (Goettingen)       
 39: Germany (Frankfurt)           40: Germany (Münster)         
 41: Greece                        42: Hungary                    
 43: Iceland                       44: India                      
 45: Indonesia (Jakarta)           46: Indonesia (Jember)         
 47: Iran                          48: Ireland                    
 49: Italy (Milano)                50: Italy (Padua)              
 51: Italy (Palermo)               52: Japan (Hyogo)              
 53: Japan (Tokyo)                 54: Japan (Tsukuba)            
 55: Korea (Seoul 1)               56: Korea (Seoul 2)            
 57: Lebanon                       58: Mexico (Mexico City)       
 59: Mexico (Texcoco)              60: Netherlands (Amsterdam)    
 61: Netherlands (Utrecht)         62: New Zealand                
 63: Norway                        64: Philippines                
 65: Poland                        66: Portugal                   
 67: Russia                        68: Singapore                  
 69: Slovakia                      70: South Africa (Cape Town)   
 71: South Africa (Johannesburg)   72: Spain (A Coruña)           
 73: Spain (Madrid)                74: Sweden                     
 75: Switzerland                   76: Taiwan (Chungli)           
 77: Taiwan (Taichung)             78: Taiwan (Taipei)            
 79: Thailand                      80: Turkey                     
 81: UK (Bristol)                  82: UK (Cambridge)             
 83: UK (London)                   84: UK (London)                
 85: UK (St Andrews)               86: USA (CA 1)                 
 87: USA (CA 2)                    88: USA (IA)                   
 89: USA (IN)                      90: USA (KS)                   
 91: USA (MD)                      92: USA (MI)                   
 93: USA (MO)                      94: USA (OH)                   
 95: USA (OR)                      96: USA (PA 1)                 
 97: USA (PA 2)                    98: USA (TN)                   
 99: USA (TX 1)                   100: USA (WA 1)                 
101: USA (WA 2)                   102: Venezuela                  
103: Vietnam                      

Selection: 20
试开URL’http://mirror.bjtu.edu.cn/cran/src/contrib/ggplot2_1.0.0.tar.gz'
Content type 'application/octet-stream' length 2351447 bytes (2.2 Mb)
打开了URL
==================================================
downloaded 2.2 Mb

* installing *source* package ‘ggplot2’ ...
** 成功将‘ggplot2’程序包解包并MD5和检查
** R
** data
*** moving datasets to lazyload DB
** inst
** preparing package for lazy loading
** help
*** installing help indices
** building package indices
** installing vignettes
** testing if installed package can be loaded
* DONE (ggplot2)

下载的程序包在
	‘/tmp/RtmpnyI2Yi/downloaded_packages’里
更新'.Library'里的HTML程序包列表
Making 'packages.html' ... 做完了。
> 

```