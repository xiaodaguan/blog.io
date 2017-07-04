---
layout: post
title: Bloom Filter 学习笔记
date: 2017-06-02
categories: blog
tags: [bloomfilter, 海量url去重]
description: 
---

## 1简介
概率型数据结构，用于判断元素是否在集合里，有误报率


## 2适用场景
空间、时间效率要求高，对误报率有一定容忍的场景


## 3概念

- 位阵列：bit array，每个位初始化0
- m: 位阵列槽个数
- hash function: 将元素hash到m个槽位其中的一个位置
- k: hash function的个数
- n: 元素总数

add(元素)：用k个hash function将元素映射m个位置中的k个，将这k个槽位置1
contains(元素)：用k个hash function将元素映射m个位置中的k个，若这k个位置上全为1，则判定元素已经在集合中

误判：元素不在集合中，却得到k个位置全为1的结果
p: 误判率

## 4误判率计算

1. 假定bloomfilter中hash function满足simple uniform hashing假设：每个元素都等概率的hash到m个槽中的一个，与其他元素无关
2. 若：m为槽个数，n为元素总数，k为hash function个数，p为误判率，则：
   对某一特定槽位：
   一个元素插入时，没有将该槽位置1的概率：1-1/m
   k个hash function都没有将其置1：(1-1/m)^k
   插入n个元素，都没有将其置1：(1-1/m)^kn
   反之，被置1概率：1-(1-1/m)^kn

3. 当查询时：
   判断元素在集合中：对一个元素进行k个hash，得到k个位置，其中的数值全部为1
4. 在将集合的元素插入bloom filter时，存在位阵列的各个槽位上重复置1的情况，即存在重合情况
5. 误判：对于一个元素进行k个hash，得到的k个位置全部存在重合情况，概率为：(1-(1-1/m)^kn)^k

6. 由于(1+x)^1/x ~ e, (1-(1-1/m)^kn)^k ~ (1-e^-nk/m)k
   可以看出，m增大或者n减小时，误判率都会降低。

7. 求极值
   假设给定m和n，则可以设b = e^n/m计算k取何值时，使得f(k) = (1-b^-k)k最小

计算过程略

可得k = 0.7m/n时p最小，此时p = 0.6185^m/n
可见若想使得误判率不变，m和n为线性同步增减关系

## 5bloomfilter应用

接受参数：元素个数n，可接受的误判率p

由上述计算可知，通过n和p可以计算得m，通过m和n可计算得k
m = -nlnP/(ln2)^2
k = 0.7m/n

