---
layout: post
title: JPA对原生SQL支持
category: program
tags: [jpa]
---

#前期准备
开发环境的搭建工作完全可以参考[jpa小例子](http://fee.im/post/2014-6-12/1)。我们直接对前期使用的Person表进行操作，直接操作SQL我们直接在单元测试中展示。
#代码

```
package com.nova.bean;

import java.util.List;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;
import javax.persistence.Query;

import org.junit.Test;

public class NativeQueryTest {

	@Test
	public void checkTable() {
		// 可以验证生成表是否正确
		EntityManagerFactory factory = Persistence
				.createEntityManagerFactory("myJpa");
		factory.close();
	}

	/**
	 * 插入数据
	 * 
	 * 这里在实现时忘记了开启事物，在对数据库进行insert、update、delete时必须开启事物进行操作
	 */
	@Test
	public void create() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		em.getTransaction().begin();
		String sql = "insert into Person(context,name) values(?,?)";
		em.createNativeQuery(sql).setParameter(1, "this is a test!")
				.setParameter(2, "test").executeUpdate();
		em.getTransaction().commit();
		em.close();
		emf.close();
	}

	/**
	 * 查询一条记录
	 */
	@Test
	public void readOne() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		String sql = "select * from Person";
		Query query = em.createNativeQuery(sql, Person.class);
		Person person = (Person) query.getSingleResult();
		System.out.println(person.getName() + "--" + person.getContext());
		em.close();
		emf.close();
	}

	/**
	 * 查询一个列表
	 */
	@Test
	public void readList() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		String sql = "select * from Person";
		Query query = em.createNativeQuery(sql, Person.class);
		@SuppressWarnings("unchecked")
		List<Person> list = query.getResultList();
		for (Person person : list) {
			System.out.println(person.getName() + "--" + person.getContext());
		}
		em.close();
		emf.close();
	}

	/**
	 * 查询表的某一列
	 */
	@Test
	public void readOneColumn() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		String sql = "select p.name from Person p";
		Query query = em.createNativeQuery(sql);
		@SuppressWarnings("unchecked")
		List<String> list = query.getResultList();
		for (String str : list) {
			System.out.println(str);
		}
		em.close();
		emf.close();
	}

}

```