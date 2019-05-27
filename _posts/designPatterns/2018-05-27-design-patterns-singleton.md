---
layout: post
title: 设计模式--单例模式
tags: [java,设计模式]
---

### 核心作用 ###

保证一个类只有一个实例，并且提供一个访问该类实例的方法。由于只生产一个实例，减少了系统性能开销。

### 应用场景 ###

* windows 任务管理器
* 数据库连接池的设计
* Spring中的Bean
* 网站的计数器功能

### 代码示例 ###

单例模式主要有饿汉式、懒汉式两种实现方式：

* 饿汉式：线程安全，调用效率高，不能延时加载；
* 懒汉式：线程安全（加锁），调用效率不高，可以延时加载；

饿汉式代码如下：

```
/**
 * 饿汉式单例模式
 *
 * @author mac
 * @date 2018-05-25 16:23
 */
public class SingletonDemo1 {
    private static SingletonDemo1 instance = new SingletonDemo1();
    private SingletonDemo1(){

    }

    public static SingletonDemo1 getInstance(){
        return instance;
    }
}

```

饿汉式单例模式代码中，static变量会在类装载时进行初始化，此时不存在多线程访问的情况，天然安全。

懒汉式代码如下：

```

/**
 * 懒汉式单例模式
 *
 * @author mac
 * @date 2018-05-25 16:23
 */
public class SingletonDemo2 {
    private static SingletonDemo2 instance ;
    private SingletonDemo2(){}

    public synchronized static SingletonDemo2 getInstance(){
        if(instance == null){
            return new SingletonDemo2();
        }
        return instance;
    }
}

```

懒汉式单例模式，非线程安全，需要使用使用synchronized关键字来保证线程安全。