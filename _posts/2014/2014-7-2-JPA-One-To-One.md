---
layout: post
title: JPA之OneToOne例子
category: program
tags: [jpa]
---

#前期准备
开发环境的搭建工作完全可以参考[jpa小例子](http://fee.im/post/2014-6-12/1)。
该程序使用人和身份证进行说明
#代码
下面是user班级的类

```
package com.nova.bean;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToOne;

@Entity
public class User {
	@Id
	@GeneratedValue
	private Integer id;
	@Column(length = 20, nullable = false)
	private String name;
	@Column(length = 5)
	private Gender gender = Gender.MAN;

	@OneToOne(optional = false, cascade = { CascadeType.ALL })
	@JoinColumn(name = "id_card")
	private IDCard idCard;

	public IDCard getIdCard() {
		return idCard;
	}

	public void setIdCard(IDCard idCard) {
		this.idCard = idCard;
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

	public Gender getGender() {
		return gender;
	}

	public void setGender(Gender gender) {
		this.gender = gender;
	}
}

```

下面是IDCard类

```
package com.nova.bean;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.OneToOne;

@Entity
public class IDCard {
	@Id
	@GeneratedValue
	private Integer id;
	@Column(length = 20)
	private String cardId;

	@OneToOne(cascade = { CascadeType.MERGE, CascadeType.REFRESH }, mappedBy = "idCard")
	private User user;

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getCardId() {
		return cardId;
	}

	public void setCardId(String cardId) {
		this.cardId = cardId;
	}
}


```

下面是单元测试

```
package com.nova.bean;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

import org.junit.Test;

public class OneToOneTest {
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

		User user = new User();
		user.setName("小小");
		user.setGender(Gender.WOMAN);

		IDCard idcard = new IDCard();
		idcard.setCardId("1234567890");

		user.setIdCard(idcard);

		em.persist(user);

		em.getTransaction().commit();
		em.close();
		emf.close();
	}
}


```