---
layout: post
title: ReentrantLock vs Synchronized
date: 2018-01-23
categories: blog
tags: [java,多线程]
description:
---



## ReentrantLock vs Synchronized

﻿都是可重入锁，通过一个计数器

非公平锁(ReetrantLock可以设置公平)

## 不同点

#### synchronized

jvm 实现

自动解锁


竞争激烈情况下性能会大幅下降

无法中断正在等候获取一个锁的线程

无法通过投票获得锁

悲观锁

#### reentrantlock

jdk实现

可实现公平锁

更精确的线程语义

投票锁、定时锁、等候和中断锁

激烈竞争下更好的性能

必须在finally中手动释放锁

乐观锁
