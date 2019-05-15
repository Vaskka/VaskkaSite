title: OS-5
author: Vaskka
tags:
  - note
  - OS
categories:
  - notes
date: 2019-05-15 13:51:00
---
# 虚拟内存-续

> 调度算法

## Fetch Policy

> 读取策略

+ Demand Paging： 请求分页式
+ Prepaging：预约分页式

### demand paging

+ 引用到某个页才加载到内存中。
+ 程序刚启动时会经常引发缺页。

### prepaging

## Replacement Policy

> 放置策略：申请的内存应该放在什么位置

## Placement Policy

> 置换策略：缺页时怎么置换、置换哪个页的算法。

### 帧锁定Frame Locking

一些重要的数据结构或内核运行使用Page或IO缓存的结构进行锁定。

实现：在每个帧加一个有效位。

#### Algorithms

##### Optimal policy （OPT 最佳）

下次访问距离当前时间最长的页置换出去。

> 前提：知道未来会访问哪个Frame

##### Least Recently Used（LRU）

上次访问距离当前时间最长的页（最长时间不使用，把这个frame换出去）

##### FIFO

> round-robin

最先分配的frame最先置换出去。

##### Clock Policy

> 时钟策略

用userbit记录是否被使用过。缺页的时候可以将userbit为0的frame牺牲。

寻找的时候每个搜索过的frame的userbit更新。（保证每次寻找都能找到userbit为0的frame）

#### 替换范围

+ 局部替换

+ 全局替换

## 加载控制 Load Control

+ 进程挂起
+ 占用空间最大的进程、驻留集最小的进程牺牲

## Linux虚拟内存管理

### introduce

三级页表

+ 页表目录
+ 下级页表目录
+ 真正页表

### 地址结构

+ golbal
+ middle
+ Page Table
+ Offset

### 内核空间分配

+ buddy system
+ slub

### Copy-on-Write

> 写时拷贝技术

系统调用
```c
vfork()
```