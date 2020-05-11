---
layout: post
title: 设计模式--代理模式[4]
tags: [java,设计模式]
---

### 核心作用 ###

通过代理控制对对象的访问，可以详细控制访问某个对象的方法，在调用这个方法前做前置处理，调用这个方法后做后置处理。

AOP的核心实现机制。

代理模式分为三个核心角色：抽象角色、真实角色、代理角色。  
抽象角色：定义代理角色和真实角色的公共对外方法；  
真实角色：实现抽象角色，定义真实角色所需要实现的业务逻辑--关注真正的业务逻辑；  
代理角色：实现抽象角色，是真实角色的代理，通过真实角色的方法来实现抽象方法，并附加自己的操作。

### 使用场景 ####

* 安全代理，屏蔽真实角色的访问
* 远程代理，通过代理类处理远程方法调用(RMI)
* Spring AOP日志拦截器
* 数据库连接池的关闭

### 静态代理模式 ###

![静态代理模式](/images/design.patterns.proxy.static.png)

```

/**
 * 抽象角色
 *
 * @author mac
 * @date 2019-05-25 21:34
 */
public interface Star {
    //面谈
    void confer();
    //签合同
    void signContact();
    //唱歌
    void sing();
    //收钱
    void collectMoney();
}


/**
 * 真实角色，实现抽象类，关注真实业务逻辑
 *
 * @author mac
 * @date 2019-05-25 21:36
 */
public class RealStar implements Star {
    @Override
    public void confer() {
        System.out.println("RealStar confer");
    }

    @Override
    public void signContact() {
        System.out.println("RealStar signContact");
    }

    @Override
    public void sing() {
        System.out.println("RealStar sing");
    }

    @Override
    public void collectMoney() {
        System.out.println("RealStar collectMoney");
    }
}

/**
 * 代理角色，通过真实角色实现抽象方法
 *
 * @author mac
 * @date 2019-05-25 21:39
 */
public class ProxyStar implements Star {
    private Star star;

    public ProxyStar(Star star) {
        this.star = star;
    }

    @Override
    public void confer() {
        System.out.println("ProxyStar confer");
    }

    @Override
    public void signContact() {
        System.out.println("ProxyStar signContact");
    }

    @Override
    public void sing() {
        star.sing();
    }

    @Override
    public void collectMoney() {
        System.out.println("ProxyStar collectMoney");
    }
}


public class ProxyTest {
    public static void main(String[] args) {
        Star star = new RealStar();
        Star proxyStar = new ProxyStar(star);

        proxyStar.confer();//面谈
        proxyStar.signContact();//签合同
        proxyStar.sing();//唱歌，针对客户而已是代理来负责唱歌，实际是明星
        proxyStar.collectMoney();//收钱
    }
}

```

### 动态代理模式 ###

动态代理可以使用JDK自带的代理来实现

![动态代理模式](/images/design.patterns.proxy.dynamic.png)

```

/**
 * 代理角色
 *
 * @author mac
 * @date 2019-05-25 21:34
 */
public interface Star {
    //面谈
    void confer();
    //签合同
    void signContact();
    //唱歌
    void sing();
    //收钱
    void collectMoney();
}

/**
 * 真实角色
 *
 * @author mac
 * @date 2019-05-25 21:36
 */
public class RealStar implements Star {
    @Override
    public void confer() {
        System.out.println("RealStar confer");
    }

    @Override
    public void signContact() {
        System.out.println("RealStar signContact");
    }

    @Override
    public void sing() {
        System.out.println("RealStar sing");
    }

    @Override
    public void collectMoney() {
        System.out.println("RealStar collectMoney");
    }
}

/**
 * JDK自带实现代理角色
 *
 * @author mac
 * @date 2019-05-25 21:59
 */
public class StarHandler implements InvocationHandler{
    Star realStar;

    public StarHandler(Star realStar) {
        this.realStar = realStar;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object object = null;
        if(method.getName().equals("sing")){
            object = method.invoke(realStar,args);
        }
        return object;
    }
}
public class DynamicProxyTest {
    public static void main(String[] args) {
        Star realStar = new RealStar();
        StarHandler starHandler = new StarHandler(realStar);

        Star proxyStar = (Star) Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(),new Class[]{Star.class},starHandler);

        proxyStar.signContact();
        proxyStar.sing();
    }
}


```



