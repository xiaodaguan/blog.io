---
layout: post
title: 线程池笔记
date: 2017-05-03
categories: blog
tags: [线程池]
description: 
---

## ExecutorService

- newFixedThreadPool(nThreads)
    - nThreads 线程池的pool size
    - .submit() 立即提交一个task
    - .shutdown() 关闭线程入口，之后无法再向线程池中提交，之前提交的会继续执行(包括active threads 和queued tasks )
    如果不关闭，进程不会结束
    - .shutdownNow() 立即停止，active threads会继续执行，queued tasks 返回一个list
    之后的f.get()会一直等待，无法结束
    - f.cancel()可以结束等待


## Future


- .get() 阻塞当前线程，等待结果
- .get(timeout,unit) 设置等待的时间，达到timeout之后，继续执行当前线程(并不会取消f那个线程的执行，依旧会存活下去)