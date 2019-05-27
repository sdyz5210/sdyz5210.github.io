---
layout: post
title: 设计模式--组合模式[5]
tags: [java,设计模式]
---

### 核心作用 ###

把整体和部分的关系用属性结构来表示，从而使客户端可以使用统一的方式处理部分对象和整体对象。

组合模式的核心：抽象构建角色：定义叶子和容器构建的共同点；叶子构建角色：无子节点；容器构建角色：有容器特定，可以包含子节点.

![组合模式](/images/design.patterns.composite.png)

### 使用场景 ####

* 组织结构的实现
* GUI的容器层次图
* Junit单元测试框架

### 组合模式代码 ###

```

/**
 * 组合模式--抽象构建
 *
 * @author mac
 * @date 2019-05-26 16:04
 */
public interface AbstractFile {
    //杀毒
    void killVirus();
}

/**
 * 组合模式--叶子构件
 *
 * @author mac
 * @date 2019-05-26 16:06
 */
public class ImageFile implements AbstractFile {
    private String name;

    public ImageFile(String name) {
        this.name = name;
    }

    @Override
    public void killVirus() {
        System.out.println("查杀图片文件:"+name);
    }
}

/**
 * 组合模式--叶子构件
 *
 * @author mac
 * @date 2019-05-26 16:06
 */
public class TextFile implements AbstractFile {
    private String name;

    public TextFile(String name) {
        this.name = name;
    }

    @Override
    public void killVirus() {
        System.out.println("查杀文本文件:"+name);
    }
}

/**
 * 组合模式--容器构件
 *
 * @author mac
 * @date 2019-05-26 16:12
 */
public class Folder implements AbstractFile {
    private String name;
    private List<AbstractFile> list = new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    public void add(AbstractFile f){
        list.add(f);
    }

    public void remove(AbstractFile f){
        list.remove(f);
    }

    public AbstractFile getChild(int index){
        return list.get(index);
    }

    @Override
    public void killVirus() {
        System.out.println("查杀文件夹:"+name);
        for(AbstractFile f:list){
            f.killVirus();
        }
    }
}

public class CompositeTest {
    public static void main(String[] args) {
        AbstractFile f1 = new ImageFile("photo.jpg");
        AbstractFile f2 = new TextFile("text.txt");
        Folder f3 = new Folder("My Folder");

        f1.killVirus();
        System.out.println("-------------------");
        f2.killVirus();
        System.out.println("-------------------");
        f3.add(f1);
        f3.add(f2);
        f3.killVirus();
        System.out.println("-------------------");
        Folder f4 = new Folder("Movie Foler");
        AbstractFile f5 = new TextFile("lova.avi");
        f4.add(f5);
        f3.add(f4);
        f3.killVirus();
    }
}

```