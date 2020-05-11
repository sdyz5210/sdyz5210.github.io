---
layout: post
title: 设计模式--观察者模式[10]
tags: [java,设计模式]
---


### 核心作用 ###

观察者模式主要用于1：N的通知。当一个对象的状态变化时，他需要及时告知一些列对象，另他们做出相应。通知观察者的方式有推、拉。

### 使用场景 ####

* 资讯订阅等功能

### 观察者代码 ###

![观察者模式](/images/design.patterns.observer.png)

```

/**
 * 观察者
 *
 * @author mac
 * @date 2019-05-26 17:54
 */
public interface ObServer {
    void update(Subject subject);
}

/**
 * 主题对象
 *
 * @author mac
 * @date 2019-05-26 17:55
 */
public class Subject {
    private List<ObServer> list = new ArrayList<>();

    public void registerObServer(ObServer obs){
        list.add(obs);
    }

    public void removeObServer(ObServer obs){
        list.remove(obs);
    }

    /**
     * 通知所有观察者更新状态
     */
    public void notifyAllObServer(){
        for(ObServer obs:list){
            obs.update(this);
        }
    }
}

/**
 * Discripition
 *
 * @author mac
 * @date 2019-05-26 18:00
 */
public class ConcreteSubject extends Subject {
    private int state;

    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
        //状态发生变化，通知所有的观察者
        this.notifyAllObServer();
    }
}

/**
 * Discripition
 *
 * @author mac
 * @date 2019-05-26 18:02
 */
public class ObServerA implements ObServer{
    //该状态和subject对象中的状态值保持一致
    private int state;

    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
    }

    @Override
    public void update(Subject subject) {
        state = ((ConcreteSubject)subject).getState();
    }
}

public class ObServerTest {
    public static void main(String[] args) {
        //创建目标对象
        ConcreteSubject cs = new ConcreteSubject();

        //创建观察者
        ObServerA ob1 = new ObServerA();
        ObServerA ob2 = new ObServerA();
        ObServerA ob3 = new ObServerA();

        //把观察者加入到目标观察者列表中
        cs.registerObServer(ob1);
        cs.registerObServer(ob2);
        cs.registerObServer(ob3);

        //改变subject的状态
        cs.setState(100);
        //观察 观察者中的状态变化
        System.out.println(ob1.getState());
        System.out.println(ob2.getState());
        System.out.println(ob3.getState());

        //改变subject的状态
        cs.setState(300);
        //观察 观察者中的状态变化
        System.out.println(ob1.getState());
        System.out.println(ob2.getState());
        System.out.println(ob3.getState());
    }
}


```

### 观察者JDK代码 ###

![观察者模式](/images/design.patterns.observer.jdk.png)

```

import java.util.Observable;

/**
 * 目标对象
 *
 * @author mac
 * @date 2019-05-26 19:30
 */
public class ConcreteSubject extends Observable {
    private int state;

    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
        setChanged();
        notifyObservers(state);
    }
}

/**
 * Discripition
 *
 * @author mac
 * @date 2019-05-26 19:35
 */
public class ObServerA implements Observer {
    private int state;

    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
    }

    @Override
    public void update(Observable o, Object arg) {
        state = ((ConcreteSubject)o).getState();
    }
}

public class ObServerTest {
    public static void main(String[] args) {
        //创建目标对象
        ConcreteSubject cs = new ConcreteSubject();

        //创建观察者
        ObServerA ob1 = new ObServerA();
        ObServerA ob2 = new ObServerA();
        ObServerA ob3 = new ObServerA();

        //把观察者加入到目标观察者列表中
        cs.addObserver(ob1);
        cs.addObserver(ob2);
        cs.addObserver(ob3);

        //改变subject的状态
        cs.setState(100);
        //观察 观察者中的状态变化
        System.out.println(ob1.getState());
        System.out.println(ob2.getState());
        System.out.println(ob3.getState());

        //改变subject的状态
        cs.setState(300);
        //观察 观察者中的状态变化
        System.out.println(ob1.getState());
        System.out.println(ob2.getState());
        System.out.println(ob3.getState());
    }
}

```