---
layout: post
title: 设计模式--策略模式[8]
tags: [java,设计模式]
---


### 核心作用 ###

策略模式对应于解决某一个问题的一个算法族，允许用户从该算法族中任选一个算法解决某一问题，同时可以方便的更换算法或者增加新的算法。并且由客户端决定调用哪一个算法。

![适配器模式](/images/design.patterns.strategy.png)

### 使用场景 ####

* Spring框架中，Resource接口资源访问策略。

### 策略代码 ###

```
/**
 * 分离算法，选择实现
 *
 * 使用场景：Spring Resource接口，资源访问策略
 * java GUI布局管理
 *
 * */
public interface IStrategy {
	void onTheWay();
}

/**
 * 策略模式上下文管理
 *
 * 负责和具体的策略类交互，算法和客户端调用分离开
 */
public class Context {
	private IStrategy strategy;

	public Context(IStrategy strategy) {
		this.strategy = strategy;
	}

	public void onTheWay() {
		this.strategy.onTheWay();
	}
}

//以下是具体的几个策略

public class CarStrategy implements IStrategy {

	@Override
	public void onTheWay() {
		System.out.println("坐汽车回家");
	}

}

public class RideStrategy implements IStrategy {

	@Override
	public void onTheWay() {
		System.out.println("骑自行车回家");
	}

}

public class WalkStrategy implements IStrategy {

	@Override
	public void onTheWay() {
		System.out.println("走路回家");
	}

}

public class GoHomeTest {

	public static void main(String[] args) {
		Context context = null;
		// 走路回家
		context = new Context(new WalkStrategy());
		context.onTheWay();
		// 坐车回家
		context = new Context(new CarStrategy());
		context.onTheWay();
		// 骑车回家
		context = new Context(new RideStrategy());
		context.onTheWay();
	}
}

```