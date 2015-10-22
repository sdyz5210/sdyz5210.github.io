---
layout: post
title: JPA中Bean对象的状态
category: program
tags: [jpa]
---

再JPA小例子中，提到了什么托管、什么游离状态，弄得我一头雾水，具体google了一下顿时明白了。

JPA中bean有四种状态：新建、托管、游离、删除。

1. 当`new Person();`对象是处于新建状态；
2. 当使用`em.getTransaction().begin();`开启事物后，执行下面的代码

```
Person person = em.find(Person.class,1); 
```

此时bean处于托管状态，这个对象发生改变EntityManager都知道。

```
em.setName("test");
em.getTransaction().commit();
```

此时会去更新数据库

3. 当调用`em.clear();`等方法时，bean变成游离状态
通过调用`em.merge(person);`把数据同步到数据库

4.认为删除对象或者垃圾回收掉