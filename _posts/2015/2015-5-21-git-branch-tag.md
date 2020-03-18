---
layout: post
title: branch和tag的区别
category: program
tags: [git,github]
---

 以前工作代码都是在svn上进行管理，在svn上也没有过多的关注过branch和tag，甚至branch和tag的作用还有一些模糊。最近学习使用了一下github，同样遇到了branch和tag，不由地了解了一下。

 branch是工程需要并行开发不同版本而创建的。如一个原型项目完成后，可能有不同的客户购买并定制，于是就需要在这个原型上构建两个独立的开发库，各自并行开发不同客户的需要。这样，branch可以是进程中的工程，而且之后会不断修改的。

tag多用于建立里程碑。比如开发达到某中程度，发布某个版本，比如v1.0，可以使用tag标注。这样，以后对于程序版本号就可以找到对应的代码状态，并进行build等操作。
理论上，tag作为里程碑的镜像存储，应该是只读的才对。

由此，可以看出tag和branch的差别。tag更重要的是记录某个里程碑，只是希望得到那个状态时的代码状态，这对bug的确认和查找很有用处。而各个branch之间是可以肆意各自的改动，互不相干的，branch上也可以有自己的tag。