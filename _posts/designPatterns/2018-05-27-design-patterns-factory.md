---
layout: post
title: 设计模式--工厂模式[3]
tags: [java,设计模式]
---

### 核心作用 ###

工厂模式实现了创建者和使用者的分离。详细分类有简单工厂模式、工厂方法模式、抽象工厂模式。

通过工厂方法代替new来创建对象，

### 使用场景 ###

* JDBC Connection对象的获取
* Spring IOC中Bean的管理

### 简单工厂模式 ###

也叫静态工厂模式。工厂类一般使用静态方法，通过传递不同的参数返回不同的对象。

![简单工厂模式](/images/design.patterns.factory.simple.png)

```

/**
 * Discripition
 *
 * @author mac
 * @date 2019-05-25 20:16
 */
public interface Car {
    void run();
}

public class Benz implements Car {
    @Override
    public void run() {
        System.out.println("Benz is running!");
    }
}

public class Audi implements Car{
    @Override
    public void run() {
        System.out.println("Audi is running!");
    }
}

//形式一
public class SimpleCarFactory {
    public static Car getCar(String type){
        if("Audi".equals(type)){
            return new Audi();
        }else if ("Benz".equals(type)){
            return new Benz();
        }else {
            return null;
        }
    }
}

//形式二
public class SimpleCarFactory2{
    public static Car getAudi(){
        return new Audi();
    }

    public static Car getBenz(){
        return new Benz();
    }
}

//客户端测试使用
public class SimpleFactoryTest {
    public static void main(String[] args) {
        Car car1 = SimpleCarFactory.getCar("Audi");
        Car car2 = SimpleCarFactory.getCar("Benz");
        car1.run();
        car2.run();
    }
}


```

### 工厂方法模式 ###

简单工厂模式只有一个工厂类，工厂方法模式有实现相同接口的一组工厂类。

在实际应用中，使用简单工厂模式较多。

![工厂方法模式](/images/design.patterns.factory.method.png)

```
public interface Car {
    void run();
}

public class Benz implements Car {
    @Override
    public void run() {
        System.out.println("Benz is running!");
    }
}

public class Audi implements Car{
    @Override
    public void run() {
        System.out.println("Audi is running!");
    }
}

public interface CarFactory {
     Car createCar();
}

public class BenzFactory implements CarFactory {
    @Override
    public Car createCar() {
        return new Benz();
    }
}

public class AudiFactory implements CarFactory {
    @Override
    public Car createCar() {
        return new Audi();
    }
}

/**
 * 方法工厂类模式
 *
 * 可以通过扩展具体实力类工程，不影响原有的业务代码
 *
 * @author mac
 * @date 2019-05-25 20:48
 */
public class MethodFactoryTest {
    public static void main(String[] args) {
        Car car1 = new AudiFactory().createCar();
        Car car2 = new BenzFactory().createCar();
        car1.run();
        car2.run();
    }
}


```


### 抽象工厂模式 ###

略。