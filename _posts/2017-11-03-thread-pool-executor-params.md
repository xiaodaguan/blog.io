---
layout: post
title: Java ThreadPoolExecutor 参数小试
date: 2017-11-03
categories: blog
tags: [java,多线程]
description:
---
## ThreadPoolExecutor 参数


new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, timeUnit, workQueue)

必须参数：

int corePoolSize: 核心线程容量//优先执行
int maximumPoolSize: 最大线程池容量
keepAliveTime: 空闲线程存活时间
timeUnit: keepAliveTime的单位
workQueue: 等待执行的线程队列

还有两个参数，一般不需要指定：

threadFactory: 创建线程用

handler: 拒绝任务用



执行过程：
1. 首先创建线程(核心员工)执行任务，直到corePoolSize满
2. 此时提交的任务被放入workQueue，直到workQueue满
3. 此时提交的任务开启新的线程(临时工)执行，直到maximumPoolSize满
4. 再提交的任务拒绝

``` java
public class ThreadPoolTest {


  public static void main(String[] args) {

    ThreadPoolExecutor poolExecutor = new ThreadPoolExecutor(2, 6, 0l, TimeUnit.MILLISECONDS, new LinkedBlockingDeque<>(3));

    for (int i = 0; i < 10; i++) {
      try {
        poolExecutor.execute(new TestRun("runner{" + i + "}"));
      } catch (Exception e) {
        System.err.println("[Exception]thread" + i + " is rejected!");
      }
    }
  }


  static class TestRun implements Runnable {

    private String name;

    public TestRun(String name) {
      this.name = name;
    }

    @Override
    public void run() {
      System.out.println("this is " + name);
      try {
        Thread.sleep(1000 * 2);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
  }

}
```

执行结果：

```
this is runner{0}
[Exception]thread9 is rejected!
this is runner{1}
this is runner{5}
this is runner{6}
this is runner{7}
this is runner{8}
this is runner{2}
this is runner{3}
this is runner{4}

```


能被执行的线程数量为：maximumPooSize + workQueue.capacity (thread9被拒绝了)

优先级顺序为：core > maximum - core > queue


## 题外

fixedThreadPool(1)与singleThreadPool()区别

fixedThreadPool可以重新设置corePoolSize，singleThreadPool不能

```
    ExecutorService poolExecutor = Executors.newFixedThreadPool(1);
    ExecutorService poolExecutor1 = Executors.newSingleThreadExecutor();
    ((ThreadPoolExecutor)poolExecutor).setCorePoolSize(3);
    ((ThreadPoolExecutor)poolExecutor1).setCorePoolSize(3);
```

```
Exception in thread "main" java.lang.ClassCastException: java.util.concurrent.Executors$FinalizableDelegatedExecutorService cannot be cast to java.util.concurrent.ThreadPoolExecutor
```
    