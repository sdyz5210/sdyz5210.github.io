---
layout: post
title: 使用maven创建springmvc的例子
category: program
tags: [maven]
---

本博文使用的源程序[源程序](https://github.com/sdyz5210/web/tree/v3.0/springweb)，该项目使用Maven进行管理。程序的访问地址为：http://localhost:8080/springweb/。

注意：本文暂时没有实现底层数据库的支持。

1、首先创建一个web项目，该博文默认大家都会了。
2、加入jar支持，配置pom.xml

本文本计划同时实现数据库层面的学习，本例子只是增加了jar的支持，但是没有实现具体底层的功能。

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.celloud.spring.web</groupId>
	<artifactId>springweb</artifactId>
	<packaging>war</packaging>
	<version>1.0-SNAPSHOT</version>
	<name>springweb Maven Webapp</name>
	<url>http://maven.apache.org</url>

	<properties>
		<javax.servlet.version>2.0</javax.servlet.version>
		<spring.version>4.2.2.RELEASE</spring.version>
		<junit.version>4.11</junit.version>
		<jdk.version>1.7</jdk.version>
		<mybatis.version>3.3.1</mybatis.version>
		<mysql.version>5.1.13</mysql.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>${spring.version}</version>
        </dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${spring.version}</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>${mysql.version}</version>
		</dependency>
		<!-- jsp-api的依赖，在配置到tomcat前，可以直接编译项目，scope设置为provided，不会被打包到war -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jsp-api</artifactId>
			<version>${javax.servlet.version}</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
			<version>1.2</version>
		</dependency>
		<dependency>
			<groupId>taglibs</groupId>
			<artifactId>standard</artifactId>
			<version>1.1.2</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-core</artifactId>
			<version>2.8.3</version>
		</dependency>
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-databind</artifactId>
			<version>2.8.3</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>${mybatis.version}</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.2.0</version>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>${junit.version}</version>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<build>
		<finalName>springweb</finalName>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.1</version>
				<configuration>
					<source>${jdk.version}</source>
					<target>${jdk.version}</target>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>

```

3、配置spring-servlet.xml

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
   http://www.springframework.org/schema/beans     
   http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
   http://www.springframework.org/schema/context 
   http://www.springframework.org/schema/context/spring-context-4.2.xsd
   http://www.springframework.org/schema/tx 
   http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
   http://www.springframework.org/schema/aop
   http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
   http://www.springframework.org/schema/mvc 
   http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd">

	<!-- 上面的命名空间如果配置不当，会报相关的错误 -->
	<!-- 引入jdbc的配置 -->
	<bean id="propertyConfigurer"
		class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<property name="locations">
			<list>
				<value>classpath:jdbc.properties</value>
				<!-- 这里可以配置多个 -->
			</list>
		</property>
	</bean>

    <!-- 注意jdbc的参数配置都需要${} -->
	<bean id="dataSource"
		class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName">
			<value>${jdbc.driverClassName}</value>
		</property>
		<property name="url">
			<value>${jdbc.url}</value>
		</property>
		<property name="username">
			<value>${jdbc.username}</value>
		</property>
		<property name="password">
			<value>${jdbc.password}</value>
		</property>
	</bean>

	<!-- 配置sqlSessionFactory -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 数据库连接池 -->
		<property name="dataSource" ref="dataSource" />
		<!-- 加载Mybatis全局配置文件 -->
		<property name="mapperLocations"
			value="classpath:com/celloud/spring/web/mapper/*.xml" />
	</bean>

	<!-- 配置mapper扫描器 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<!-- 扫描包路径，如果需要扫描多个包中间用半角逗号隔开 -->
		<property name="basePackage" value="com.celloud.spring.web.mapper"></property>
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
	</bean>

	<!-- 事务控制 对MyBatis操作数据库 spring使用JDBC事务控制类 -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 配置数据源 -->
		<property name="dataSource" ref="dataSource" />
	</bean>

	<!-- 通知，这里的id=txAdvice和下面aop要对应 -->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 传播行为 -->
			<tx:method name="save*" propagation="REQUIRED" />
			<tx:method name="insert*" propagation="REQUIRED" />
			<tx:method name="update*" propagation="REQUIRED" />
			<tx:method name="delete*" propagation="REQUIRED" />
			<tx:method name="find*" propagation="SUPPORTS" read-only="true" />
			<tx:method name="select*" propagation="SUPPORTS" read-only="true" />
		</tx:attributes>
	</tx:advice>
	<!-- 只对业务逻辑(service)层实施事务 -->
	<aop:config>
		<aop:pointcut id="transactionPointcut"
			expression="execution(* com.celloud.spring.web.service.*.*(..))" />
		<aop:advisor pointcut-ref="transactionPointcut"
			advice-ref="txAdvice" />
	</aop:config>

	<context:component-scan base-package="com.celloud.spring.web" />

	<!-- ViewResolver -->
	<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsp/" />
		<property name="suffix" value=".jsp" />
	</bean>

	<!-- 静态资源 -->
	<mvc:resources mapping="/html/**" location="/WEB-INF/html/" />
	<!-- 得到MVC注解驱动特性 -->
	<mvc:annotation-driven />
</beans>

```

其他代码请参考github的源码[源码](https://github.com/sdyz5210/web/tree/v3.0/springweb)