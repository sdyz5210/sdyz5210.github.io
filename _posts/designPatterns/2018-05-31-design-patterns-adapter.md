---
layout: post
title: 设计模式--适配器模式[7]
tags: [java,设计模式]
---

### 核心作用 ###

将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以在一起工作。

适配器模式中的角色：目标接口：客户所期待的接口，目标可以是具体的或抽象的类，也可以是接口。需要适配的类（Adaptee）：需要适配的类或适配者类。适配器（Adapter）：通过包装一个需要适配的对象，把原接口转换成目标接口。　

![适配器模式](/images/design.patterns.adapter.png)

### 使用场景 ####

* 旧系统的改造升级

### 适配器模式代码 ###


```

/**
 * 目标接口
 *
 * @author mac
 * @date 2019-05-26 20:01
 */
public interface Target {
    void handlerReq();
}

/**
 * 被适配的类
 *
 * @author mac
 * @date 2019-05-26 19:59
 */
public class Adaptee {
    public void request(){
        System.out.println("可以完成客户需要的功能");
    }
}

/**
 * 适配器
 *
 * @author mac
 * @date 2019-05-26 20:03
 */
public class Adapter implements Target{
    private Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public void handlerReq() {
        adaptee.request();
    }
}

public class Client {
    public void test(Target t){
        t.handlerReq();
    }

    public static void main(String[] args) {
        Adaptee adaptee = new Adaptee();
        Target t = new Adapter(adaptee);
        Client client = new Client();
        client.test(t);
    }
}

```