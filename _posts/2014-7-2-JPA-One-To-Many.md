---
layout: post
title: JPA之OneToMany和ManyToOne例子
category: program
tags: [jpa]
---

#前期准备
开发环境的搭建工作完全可以参考[jpa小例子](http://fee.im/2014/06/12/jpa-example/)。
该程序的例子使用班级和学生进行，班级和学生的关系是一对多的关系，反过来学生和班级的关系是 多对一的关系。所以从这个例子中我们就可以学习到这两个关系的处理。
#基本知识
在OneToMany和ManyToOne使用过程中，需要我们明确两个概念：关系维护端和关系被维护端。关系维护端负责更新关系的外键等操作。在程序中被标记mappBy的表示关系被维护端，只有关系维护端有权更新外键。
数据加载方式可以根据名字进行辨别，OneToMany默认的加载方式是懒加载，从设置的关系中（OneToMany和ManyToOne）最后一个单词是Many，那么该加载默认为懒加载。
在所有的关系中OneToMany、ManyToOne、ManyToMany，以many结尾的默认加载方式都是懒加载。
#代码
下面是classes班级的类

```
package com.nova.bean;

import java.util.HashSet;
import java.util.Set;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.OneToMany;

@Entity
public class Classes {
	@Id
	@GeneratedValue
	private Integer id;
	@Column(length = 100, nullable = false)
	private String name;

    //mappedBy标记为关系被维护端，所以外键关系由student端维护。
	@OneToMany(cascade = CascadeType.ALL, mappedBy = "classes")
	private Set<Student> set = new HashSet<Student>();

	public Classes() {

	}

	public Classes(String name) {
		this.name = name;
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

	public Set<Student> getSet() {
		return set;
	}

	public void setSet(Set<Student> set) {
		this.set = set;
	}
}

```

下面是学生类

```
package com.nova.bean;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedVamappBylue;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;

@Entity
public class Student {
	@Id
	@GeneratedValue
	private Integer id;
	@Column(length = 100, nullable = false)
	private String name;
	@Column(length = 3)
	private Integer age;
	@Column(length = 5)
	private Gender gender = Gender.MAN;

	@ManyToOne(cascade = { CascadeType.MERGE, CascadeType.REFRESH }, optional = true)
	@JoinColumn(name = "classId") //这里设置JoinColum设置了外键的名字
	private Classes classes;

	public Student() {

	}

	public Student(String name, Integer age, Gender gender) {
		this.name = name;
		this.age = age;
		this.gender = gender;
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

	public Integer getAge() {
		return age;
	}

	public void setAge(Integer age) {
		this.age = age;
	}

	public Gender getGender() {
		return gender;
	}

	public void setGender(Gender gender) {
		this.gender = gender;
	}

	public Classes getClasses() {
		return classes;
	}

	public void setClasses(Classes classes) {
		this.classes = classes;
	}
}

```

下面是单元测试

```
package com.nova.bean;

import java.util.HashSet;
import java.util.Set;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

import org.junit.Test;

public class OneToManyTest {

	@Test
	public void init() {
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

		Classes c = new Classes("一年级三班");
		Set<Student> set = new HashSet<Student>();

		Student s1 = new Student("小张", 6, Gender.MAN);
		s1.setClasses(c);
		set.add(s1);
		Student s2 = new Student("萧文", 5, Gender.WOMAN);
		s2.setClasses(c);
		set.add(s2);
		c.setSet(set);

		em.persist(c);

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

		Classes c = em.find(Classes.class, 1);
		System.out.println(c.getName());
		for (Student s : c.getSet()) {
			System.out.println(s.getName());
		}

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

		Classes c = em.find(Classes.class, 1);
		Student s = em.find(Student.class, 2);
		c.setName("三年级");
		s.setName("小张");
		s.setClasses(c);

		em.merge(c);
		em.getTransaction().commit();
		em.close();
		emf.close();
	}

	/**
	 * 删除班级和学生的所有记录
	 * 
	 * 删除班级的时候，会自动把关联学生的记录删除
	 */
	@Test
	public void delete() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		em.getTransaction().begin();

		Classes c = em.find(Classes.class, 1);
		em.remove(c);

		em.getTransaction().commit();
		em.close();
		emf.close();
	}

	/**
	 * 删除学生的记录
	 * 
	 * 删除学生时不会影响班级的数据
	 */
	@Test
	public void delete1() {
		EntityManagerFactory emf = Persistence
				.createEntityManagerFactory("myJpa");
		EntityManager em = emf.createEntityManager();
		em.getTransaction().begin();

		Student s = em.find(Student.class, 4);
		em.remove(s);

		em.getTransaction().commit();
		em.close();
		emf.close();
	}

}

```

#总结
在OneToMany和ManyToOne中主要弄清楚什么是关系维护端，什么是关系被维护端。mappBy标记的一定是关系被维护端，关系维护端负责外键的更新。