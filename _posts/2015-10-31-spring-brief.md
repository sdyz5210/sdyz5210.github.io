---
layout: post
title: spring mvc介绍
category: program
tags: [samtools,biosoft]
---

以前一直在使用ssh、ssi、guice等框架，SpringMVC也是一个比较火热的技术，一直没有实际体验过。目前在考虑某个项目的技术架构，考虑到了该技术，最近也在熟悉spring mvc方面的信息。

#简介#
Spring是一个开源框架，是为了解决企业应用程序开发复杂性而创建的。框架的主要优势之一就是其分层架构，分层架构允许您选择使用哪一个组件，同时为J2EE应用程序开发提供集成的框架。

Spring框架是一个分层架构，由7个定义良好的模块组成。Spring模块构建在核心容器之上，核心容器定义了创建、配置和管理bean的方式。组成Spring框架的每个模块或组件都可以单独存在，或者与其他一个或多个模块联合实现。

![spring框架组成图](/images/springmvc.gif)

*spring core:核心容器提供Spring框架的基本功能。核心容器的主要组件是BeanFactory，它是工厂模式的实现。BeanFactory使用控制反转（IOC）模式将应用程序的配置和依赖性规范与实际的应用程序代码分开。

*spring context:spring上下文是一个配置文件，向spring框架提供上下文信息。Spring上下文包括企业服务，例如JNDI、EJB、电子邮件、国际化、校验和调度功能。

*spring aop:通过配置管理特性，spring aop模块直接将面向方面的编程功能集成到spring框架中。所以可以很容易地使Spring框架管理的任何对象支持AOP。Spring AOP模块为了基于Spring的应用程序中的对象提供了事务管理服务。通过使用Spring AOP，不用依赖EJB组件，就可以将声明性事务管理集成到应用程序中。

*spring DAO: JDBC DAO抽象层提供了有意义的异常层次结构，可用结构来管理异常处理和不同数据库供应商抛出的错误消息。异常层次结构简化了错误处理，并且极大地降低了需要编写的异常代码数量（例如打开和关闭连接）。Spring DAO的面向JDBC的异常遵从通用的DAO异常层次结构。

*spring ORM：Spring框架插入了若干个PRM框架，从而提供了ORM得对象工具，其中包括JDO、Hibernate、Ibatis。所有这些都遵从Spring的通用事物和DAO异常层次架构。

*spring Web模块：web上下文模块建立在应用程序上下文模块之上，为基于web的应用程序提供了上下文。所以，Spring框架支持于struts的集成。web模块还简化了处理多部分请求以及将请求参数绑定到域对象的工作。

*spring mvc框架：MVC框架是一个全功能的构建web应用程序的MVC实现。通过策略接口，MVC框架变成为高度可配置的，MVC容纳了大量视图技术，其中包括JSP、Velocity、Tiles、iText和POI。

Spring 框架的功能可以用在任何 J2EE 服务器中，大多数功能也适用于不受管理的环境。Spring 的核心要点是：支持不绑定到特定 J2EE 服务的可重用业务和数据访问对象。毫无疑问，这样的对象可以在不同 J2EE 环境 （Web 或 EJB）、独立应用程序、测试环境之间重用。












