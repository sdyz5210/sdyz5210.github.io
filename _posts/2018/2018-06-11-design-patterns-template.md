---
layout: post
title: 设计模式--模板方法模式[9]
tags: [java,设计模式]
---


### 核心作用 ###

模板方法模式定义了一个操作中的算法骨架，将某些步骤延迟到子类中实现。这样新的子类可以在不改变一个算法结构的前提下重新定义该算法的某些特定步骤。

处理某个流程的代码已经都具备，但是其中某个节点的代码暂时不能确定。因此，我们采用工厂方法模式，将这个节点的代码实现转移给子类完成。即处理步骤父类中定义好，具体实现延迟到子类中来实现。


### 使用场景 ####

* Spring框架中，JDBC Template的实现
* Servlet中doGet、doPost的实现

### 模板模式代码 ###

```

/**
 * 模板方法模式
 *
 * 实现一个算法时，整体步骤固定，但是某些部分容易变化，易变化的部分可以抽象出来供子类实现。
 *
 * 使用场景：Spring jdbcTemplate;HibernateTemplate etc.
 *
 * @author mac
 * @date 2019-05-26 17:04
 */
public abstract class BankTemplateMethodAbstract {
    public void takeNum(){
        System.out.println("取号排队");
    }

    //办理具体业务
    public abstract void transact();

    public void evaluate(){
        System.out.println("反馈评分");
    }

    public final void process(){
        takeNum();
        transact();
        evaluate();
    }
}

//以下定义了两种实现方式：

public class TemplateTest {
    public static void main(String[] args) {
        BankTemplateMethodAbstract btm = new DrawMoney();
        btm.process();

        BankTemplateMethodAbstract btm2 = new BankTemplateMethodAbstract() {
            @Override
            public void transact() {
                System.out.println("我要办理理财业务");
            }
        };
        btm2.process();
    }
}

class DrawMoney extends BankTemplateMethodAbstract{
    @Override
    public void transact() {
        System.out.println("我要取款100w");
    }
}

```