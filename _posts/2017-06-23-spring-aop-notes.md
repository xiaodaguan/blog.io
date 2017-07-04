---
layout: post
title: spring aop 笔记
date: 2017-06-23
categories: blog
tags: [aop, spring]
description: spring aop 笔记
---

# spring aop 笔记

## 使用方法

1. 创建切面类
2. 声明切点

```java
 @Pointcut("execution(* common.other.HelloWorld.sayHi())")
    private void whatever(){}
```

3. 定义通知

```java
@Before
public void doBefore(){
  //...
}
```

4. 修改spring conf xml

```xml
<aop:aspectj-autoproxy/>
<bean 切面类...
<bean 被切的类...
<!-- 注意被拦截的对象实例必须通过spring创建 -->
```

## 记录

after returning: 是方法正常结束执行
after 是final
如果切点方法抛出异常，after会执行，after returning 不会



