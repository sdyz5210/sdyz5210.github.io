---
layout: post
title: 使用BasicDataSource引发的数据库连接中断的问题和解决方法
category: program 
tags: [mysql]
---

最近碰到一个问题，应用程序每天的第一次进行系统访问时，会报一个奇怪的错误，最后经过仔细的跟踪，错误信息找到了,如下所示:

```
The last packet successfully received from the server was 60,428,178 milliseconds ago.  The last packet sent successfully to the server was 60,428,180 milliseconds ago。
```

很明显，这个错误信息表示数据库连接实际上已经断开了，因为在深夜没有客户来访问系统。但是程序中并没有检测到这个信息，仍然使用过期的数据库连接，那肯定会报错。而在之后，datasource检测到这个问题，则尝试重新进行连接，这样接下来的链接就正常了。
在项目中使用的数据库连接池是dbcp，即apache-dbcp的BasicDataSource。最初的配置为:

```
<bean id="dataSourceLog" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close">
        <property name="driverClassName" value="" />
        <property name="defaultAutoCommit" value="" />
        <property name="url" value="" />
        <property name="username" value="" />
        <property name="password" value="" />
        <property name="maxActive" value=""/>
</bean>
```

经仔细分析，这样的配置不会对连接的连接超时以及有效性进行检测，因此会发生以上的问题。要解决这个问题，就需要额外的检测手段和处理方法，还好，dbcp本身提供了这些配置，只不过当前系统中并没有这样的配置。

在处理这个问题中，需要处理好以下这些问题：

*连接多少时间的数据库连接被认为是无效的
*如何检测一个连接是有效的
*如何检测那些执行SQL时间长的连接
*周期性连接检测需要多长时间执行一次

上面的这些问题，dbcp都提供了有效的配置方法，只需要详细的配置即可。如详细的配置如下:

```
//是否要进行检测
<property name="testWhileIdle" value="true"/>
//进行检测一个连接是有效的SQL语句，比如oracle是select 1 from dual 而 mysql是 select 1
<property name="validationQuery" value=""/>
//是否在数据库连接请求量大的时候，如总数50，当前已请求了49个，所剩不多了，检测那些执行时间久的连接，将其从池中移除掉(移除之后怎么作，由实现决定，如直接断开，或者任其继续执行等)
<property name="removeAbandoned" value="true"/>
//一次数据库操作执行时间超过多少秒的连接被认为是需要移除的
<property name="removeAbandonedTimeout" value=""/>
//每隔多少时间检测一次，比如每半小时检测一次，总不能总是检测，这会对性能产生影响
<property name="timeBetweenEvictionRunsMillis" value=""/>
//每次检测时，需要检测多少个数据连接，一般设置为与最大连接数一样，这样就可以检测完所有的连接
<property name="numTestsPerEvictionRun" value=""/>
//一个数据库连接连接多少时间之外，我们认为其应该不再适用了(可能下一次就会失效了)，应该移除并重新建立连接了
<property name="minEvictableIdleTimeMillis" value=""/>
```

经过这样配置之后，之前的问题就不再出现了。引起这个问题的原因是多方面的，但对于问题主因，仍是对dbcp未完全了解，总是按默认配置进行开发。在某些场景下就会突发一些想不到的问题。
此文章转载自http://www.iflym.com/index.php/code/201308280001.html