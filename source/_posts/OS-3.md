title: OS-3
author: Vaskka
tags:
  - note
  - os
categories:
  - notes
date: 2019-03-27 13:55:00
---
# 并发（续）

## 并发的困难

+ 变量共享
+ ...

## 并发相关操作

+ 原子操作
+ 临界段
+ 死锁
+ starvation

## 并发问题

### 竞争条件

维持处理器

分配资源

...

### 进程间可能的交互关系

+ Processes unaware of each other
+ Processes indirectly aware of each other
+ Process directly aware of each other

### 互斥

+ 一次只允许一个进程进入临界区
+ 阻塞于临界区外的进程不能干涉其他进程
+ 不会发生饥饿和死锁
+ 闲则让进
+ 对进程的执行速度和处理器数目没有关系
+ 有限占用

### 实现 （硬件方式）

+ 关中断（一般用户进程不会使用）
+ 特殊机器指令
	+ CAS
        ```c
        int compare_and_swap(int * word, int testval, int newval)
        {
            int oldval;
            oldval = *word;
            if (oldval == testval) {
                *word = newval;
            }

            return oldval;
        }
        ```
        CAS指令：**CMPXCHG/CMPXCHGL**

        实现：CPU北桥上HLOCK引脚电位拉低（硬件支持）

	+ exchange 指令
    	...

#### 优点

+ 实现简单

#### 缺点

+ 忙等待
+ 饥饿
+ 死锁

### 实现（算法方式）

+ Dekker's Algorithm
	+ 资源不能共享
    + 无死锁
    + 无饥饿
    + 解释：以turn做标记变量，只有自己的turn才有操作的权限。可以实现互斥。
     