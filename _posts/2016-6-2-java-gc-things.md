---
layout: post
title: java 垃圾回收机制
date: 2016-6-2
categories: blog
tags: [gc, java, jvm]
description:  

---


﻿## stop-the-world

任何一种gc算法均会调用stop-the-world，即暂停除gc需要以外的一切线程。gc优化很多时候就是要减少stop-the-world的发生

因此，

- 设置为null
没什么坏处，但也并不一定有什么作用

- System.gc()
显著影响系统性能，应彻底避免

## 分代垃圾回收机制

 弱年代假设：
- 大多数对象会很快变得不可达
- 只有很少的由老对象指向新生对象的引用


- 新生代(young generation): minor GC
- 老年代(old generation): major GC/full GC

- 此外还有持久代(permanent generation): 又称方法区，保存类常量及字符串常量。发生在此区域上的gc也被算作major GC

- card table: 
老年代的对象引用一个新生代的对象，记录在这个表中。当针对新生代gc的时候，只需查询card table来决定是否可以被收集，而不用查询老年代。

新生代的构成：

- 一个伊甸园空间(Eden)
- 两个幸存者空间(Survivor)

1. 绝大多数刚创建的对象放在eden
2. 在eden执行了第一次gc之后，存活对象被移动到一个survivor
3. 此后，在eden执行gc之后，存活对象堆积在同一个survivor
4. 当一个survivor饱和，将这个survivor中的存活对象移动到另一个survivor，然后清空这个survivor
5. 以上迭代几次后仍存活的对象移动到老年代

两种加快内存分配的技术(扩展内容)：
- bump-the-pointer
- TLABs(Thread-Local Allocation Buffers)



