---
layout: post
title: 设计模式--装饰模式[6]
tags: [java,设计模式]
---


### 核心作用 ###

装饰模式也叫做包装器模式，可以降低系统的耦合度，可以动态的增加或删除对象的职责，并使得需要装饰的具体构建类和具体装饰类可以独立变化，以便增加新的具体构建类和具体装饰类。装饰模式是一种用于代替继承的技术，无须通过继承增加子类就能扩展对象的新功能。使用对象的关联关系替代继承关系，更加另外同时避免类型体系的快速膨胀。

实现细节：  

抽象构建角色：真实对象和装修对象有相同的接口。这样客户端对象就能够以与真实对象相同的方式同装修器对象交互。

具体构建角色（真实对象）：

装饰角色：持有一个抽象构件的引用。装饰对象接受所有客户端的请求，并把这些请求转发给真实的对象。这样，就能在真实对象调用前后增加新的功能

具体装修角色：负责给构件对象增加新的责任。

![装饰模式](/images/design.patterns.decorator.png)

### 使用场景 ####

* IO中输入输出流的设计
* Swing包中图形界面构件功能

### 装饰模式代码 ###

```

/**
 * 抽象组件
 *
 * @author mac
 * @date 2019-05-26 22:01
 */
public interface ICar {
    void move();
}

/**
 * 具体构建角色--真实对象
 *
 * @author mac
 * @date 2019-05-26 22:02
 */
public class Car implements ICar {
    @Override
    public void move() {
        System.out.println("普通汽车，陆地上运行");
    }
}

/**
 * 装饰器角色
 *
 * @author mac
 * @date 2019-05-26 22:03
 */
public class SuperCar implements ICar {
    private ICar car;

    public SuperCar(ICar car) {
        this.car = car;
    }

    @Override
    public void move() {
        car.move();
    }
}

/**
 * 具体装饰对象
 *
 * @author mac
 * @date 2019-05-26 22:05
 */
public class FlyCar extends SuperCar {

    public FlyCar(ICar car) {
        super(car);
    }

    public void fly(){
        System.out.println("可以飞的汽车");
    }

    @Override
    public void move() {
        super.move();
        fly();
    }
}

/**
 * 具体装饰对象
 *
 * @author mac
 * @date 2019-05-26 22:05
 */
public class AutoCar extends SuperCar {

    public AutoCar(ICar car) {
        super(car);
    }

    public void auto(){
        System.out.println("可以自动驾驶的汽车");
    }

    @Override
    public void move() {
        super.move();
        auto();
    }
}

public class DecoratorTest {
    public static void main(String[] args) {
        Car car = new Car();
        car.move();

        System.out.println("------------------");

        FlyCar flyCar = new FlyCar(car);
        flyCar.move();

        System.out.println("------------------");

        AutoCar autoCar = new AutoCar(car);
        autoCar.move();

        System.out.println("------------------");
        autoCar = new AutoCar(flyCar);
        autoCar.move();
    }
}

```