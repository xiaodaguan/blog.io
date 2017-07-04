---
layout: post
title: 依赖注入笔记
date: 2017-5-15
categories: blog
tags: 
description: 
---﻿


##依赖
Q: 什么是依赖？

A: ClassA 中有 ClassB的实例，则称ClassA对ClassB有依赖，类对类的依赖

栗子：

```
public class Human{

    Father father;

    public Human(){
        father = new Father();
    }

}
```

> 这段代码存在的问题：
> 1. 如果需要改变father生成方式，需要修改human代码
> 2. 测试不同father对Human影响很困难，因为father对象的生成写死在了Human构造函数中
> 3. 如果new Father()过程非常缓慢，单测时我们希望用已经初始化好了的father对象Mock掉这个过程也很难

综上：最大的弊端在于，在构造函数中初始化依赖，两个类不够独立。

## 依赖注入

什么是注入：将father对象作为构造函数的一个参数传入，在调用Human的构造方法之前，外部就已经初始化好了Father对象
像这种非自己主动初始化依赖，而通过外部来传入依赖的方式，称为依赖注入。

```
public class Human{

    Father father;

    public Human(Father father){
        this.father=father;
    }
}

```


优点：解耦；方便单测

## java中的依赖注入

依赖注入有很多种途径，java中最常用的是注解
> 注解：java 1.5 提供的特性，通过注解可以给jvm提供额外的信息，可以在运行时获取，从而改变代码的行为


```
public class Human{
    @Inject Father father;
    public Human(){
    }

}
```

以上代码写了一个@Inject注解，如果想要实现注入，还需要一个依赖注入框架。
java中流行的依赖注入框架有：Google Guice, Spring 等。