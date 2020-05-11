---
layout: post
title: JPA小例子
category: program
tags: [jpa]
---

JPA是一套相当优秀的持久化规范，本例子使用的是Hibernate对JPA提供的实现。下来我们来开始体验一下。

#准备活动
下载Hibernate，直接google官网或者直接访问http://hibernate.org/orm/地址下载即可。
本例子下载的版本为：hibernate-release-4.3.5.Final
#Jar包
解压缩下载的hibernate的压缩包，打开lib目录可以看到下面左侧的图片，我们在eclipse中创建一个java project项目，导入jar文件，jar文件直接从lib目录的jpa和required两个目录拷贝即可。创建的项目图片如下右侧图片
![jpa1](/images/jpa1.png) 左--右 ![jpa2](/images/jpa2.png)
#配置
在项目的src路径下面建立META-INF目录，在该目录下面创建persistence.xml文件，具体内容如下：

```  
<persistence xmlns="http://java.sun.com/xml/ns/persistence"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/persistence http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd"
	version="2.0">
	<!-- 为持久化单元取名myjpa，事物类型我们选择本地事物 -->
	<persistence-unit name="myJpa" transaction-type="RESOURCE_LOCAL">
		<properties>
			<!-- 配置Hibernate方言 -->
			<property name="hibernate.dialect" value="org.hibernate.dialect.MySQL5Dialect" />
			<!-- 配置数据库驱动 -->
			<property name="hibernate.connection.driver_class" value="com.mysql.jdbc.Driver" />
			<!-- 配置数据库用户名 -->
			<property name="hibernate.connection.username" value="root" />
			<!-- 配置数据库密码 -->
			<property name="hibernate.connection.password" value="novacloud" />
			<!-- 配置数据库url-->
			<property name="hibernate.connection.url" value="jdbc:mysql://localhost:3306/jpa" />
			<!-- 配置显示在console显示sql -->
			<property name="hibernate.show_sql" value="true" />
			<!--  自动创建|更新|验证数据库表结构，我们这里选择update -->
			<property name="hibernate.hbm2ddl.auto" value="update" />
		</properties>
	</persistence-unit>
```

其实这个hibernate.hbm2ddl.auto参数的作用主要用于：自动创建|更新|验证数据库表结构。如果不是此方面的需求建议set value="none"。里面可以设置的几个参数：
1. validate 每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。
2. create 每次加载hibernate时都会删除上一次的生成的表，然后根据你的model类再重新来生成新表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。
3. create-drop 每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。
4. update 最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据 model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等应用第一次运行起来后才会。

#代码
**实体类**

```
package com.nova.bean;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Person {
	@Id
	@GeneratedValue
	private Integer id;
	@Column(length = 50, nullable = false)
	private String name;
	private String context;

	public Person() {
	}

	public Person(String name, String context) {
		this.name = name;
		this.context = context;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getContext() {
		return context;
	}

	public void setContext(String context) {
		this.context = context;
	}

}

```

**单元测试**

```
package com.nova.bean;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

import org.junit.Test;

public class PersonTest {

	@Test
	public void checkTable() {
		// 可以验证生成表是否正确
		EntityManagerFactory factory = Persistence
				.createEntityManagerFactory("myJpa");
		factory.close();
	}

	@Test
	public void create() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		em.getTransaction().begin();
		Person person = new Person("ingress", "this is a playgames!");
		em.persist(person);
		em.getTransaction().commit();
		em.close();
		emf.close();
	}

	@Test
	public void read() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		em.getTransaction().begin();
		Person person = em.find(Person.class, 1);
		System.out.println(person.getName() + "--" + person.getContext());
		em.getTransaction().commit();
		em.close();
		emf.close();
	}

	@Test
	public void update() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		em.getTransaction().begin();
		Person person = em.find(Person.class, 1);
		person.setContext("This is a games!");// person为托管状态
		em.merge(person);
		em.getTransaction().commit();
		em.close();
		emf.close();
	}

	@Test
	public void update1() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		em.getTransaction().begin();
		Person person = em.find(Person.class, 2);
		em.clear();// 把实体管理器中的所有实体变为脱管状态
		person.setContext("This is a games!");
		em.merge(person);// 把脱管状态变为托管状态,merge可以自动选择insert or update 数据
		em.getTransaction().commit();
		em.close();
		emf.close();
	}

	@Test
	public void delete() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		em.getTransaction().begin();
		Person person = em.find(Person.class, 1);
		em.remove(person);
		em.getTransaction().commit();
		em.close();
		emf.close();
	}

}

```