---
layout: post
title: Eclipse下安装和使用git的笔记
category: program  
tags: [eclipse,github]  
--- 

在开发java、javaee等项目时，我们经常会使用eclipse或者myeclipse等ide工具，随着互联网的发展源代码的管理工具也随着不断的变化，从开始的vss、cvs、svn到现在的分布式管理工具git，目前git作为版本控制软件是非常受大家欢迎的。所以我们工作中需要在ide上集成git插件。在eclipse中，egit是其基金会开发的一个插件。

#Egit插件安装  
首先要明确你要安装的Eclipse的版本，不同版本的ide需要安装不同的Egit。这里有eclipse和egit的对应版本：http://wiki.eclipse.org/EGit/FAQ#WherecanIfindolderreleasesof_EGit.3F。如下图展示的内容：  
![egit1](/images/egit1.png)  
上面图片显示的eclipse版本和egit版本之间的对应。  
下图是相应需要安装的url。
![egit2](/images/egit2.png)  
确定好了这些信息之后，我们可以开始安装egit插件。看下图是大家比较熟悉的eclipse的界面，在help中找到“安装新软件”菜单。  
![egit3](/images/egit3.png)  
打开后看到如下界面，因为我的eclipse是3.7版本所以我们需要安装的1.3版本，上图已经标记过。  
![egit4](/images/egit4.png)  
输入符合我们版本的url即可。  
![egit5](/images/egit5.png)  
后续一路next即可。

#github免登陆配置
在mac或者ubuntu下面我们可以配置github的免登陆，具体参考这个link：
https://help.github.com/articles/generating-ssh-keys/
配置完成后我们还需要在github上创建一个远程的版本库，目前我已经创建好了
#git使用
Mac系统下面是存在git客户端的，所以不需要单独安装。如果在windows下面需要安装git。这里不多说了。接下来我们使用git进行管理项目代码。
创建一个项目fee，这里我eclipse中同时安装maven，使用maven创建一个简单地项目，如下图：  
![egit6](/images/egit6.png)  
在项目上点击右键--team--share project菜单，选择git，按照下图中说明进行操作  
![egit7](/images/egit7.png)  
之后点击 finish完成，我们可以看到项目中的状态发生了变化。如下图：  
![egit8](/images/egit8.png)  
接下来我们就可以忘本地库中进行提交了，右键点击项目--team-commit后见下图：  
![egit9](/images/egit9.png)  
点击commit提交就 ok 了 

#github远程提交
在项目上点击右键--team--remote--push菜单  
![egit10](/images/egit10.png)  
直接点击next，如下图：
![egit11](/images/egit11.png)
接下来直接默认下一步就ok了

#说明
本人也是刚刚开始使用eclipse和git的配合，很多地方说的不是很清楚，后续使用过程中会不断优化该博文。
