title: Banker’s Algorithm随记
author: Vaskka
tags:
  - 算法
  - 随记
categories:
  - 随记
date: 2019-04-17 16:14:00
---
# 记于操作系统实验课

> 关于Banker’s Algorithm算法的过程

## 变量定义

对于n个进程$p_1...p_n$，这些进程想要访问$m$种资源。

定义矩阵$Max_{n*m}:m_{i, j}=k$表示进程$p_{i}$想要访问$k$个资源$R_{j}$

定义矩阵$Allocation_{n*m}:a_{i, j}=k$表示进程$p_{i}$已经被分配了$k$个$R_{j}$资源。

定义矩阵$Need_{n*m}:n_{i, j}=k$ 表示进程$p_{i}$还需要分配$k$个$R_{j}$资源。

## e.g.

线程占用最大资源及已经分配资源

![线程占用最大资源及已经分配资源](https://raw.githubusercontent.com/Vaskka/GitLearn/master/original.png)

Need矩阵情况

![Need矩阵](https://raw.githubusercontent.com/Vaskka/GitLearn/master/need.png)

## 算法步骤

1. 将当前的可用资源$(3, 3, 2)$与$p_0 ~ p_4$的need进程比较。

2. 比较发现$p_0$无法进行，比较$p_1$，可以执行。
	I. 首先将$need_1$的三组数从可用的资源$(3, 3, 2)$中减掉，剩余$(2, 1, 0)$
    II. 再将剩余的$(2, 1, 0)$加上$p_1$的最大资源，及完成$p_0$后的worker数量，结果是$(2, 1, 0) + (3, 2, 2) = (5, 3, 2)$

3. 用以上的规律得出剩下全部worker的值。

## 执行结果

![worker情况](https://raw.githubusercontent.com/Vaskka/GitLearn/master/worker.png)
