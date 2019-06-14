title: 操作系统五算法——Banker
author: Vaskka
tags:
  - OS
categories:
  - 期末复习
date: 2019-06-14 16:19:00
---


> 银行家算法（Banker's Algorithm）是一个避免死锁（Deadlock）的著名算法，是由艾兹格·迪杰斯特拉在1965年为T.H.E系统设计的一种避免死锁产生的算法。它以银行借贷系统的分配策略为基础，判断并保证系统的安全运行。

# 问题描述

对与$P_0,P_1,P_2,...,P_n \in Processor$同时对$n$个资源有限变量$R_0,R_1,R_2,...,R_m \in Resource$的互斥操作，目的为保证在进程运行时的安全或发生异常及时退出。

# 数据结构

+ 对于工作进程$p \in Processor$，都包含$m$个维度描述$R_1,R_2,R_3,...,R_m \in Resource$的**最大需求**。这些进程向量一起构成的矩阵称为MAX需求矩阵，计作$MAX_{n \times m}$。
+ 对于工作进程$p \in Processor$，都包含$m$个维度描述对于资源$R_1,R_2,R_3,...,R_m \in Resource$的**已经分配**资源数。这些进程向量一起构成的矩阵称为Allocation矩阵，计作$Allocation_{n \times m}$。
+ 资源$R_1,R_2,R_3,...,R_m \in Resource$描述对m个资源**当前可用**资源数。这些元素一起构成的向量称为Available向量，计作$\boldsymbol {Available}$。
+ 对于工作进程$p \in Processor$，都包含$m$个维度描述对于资源$R_1,R_2,R_3,...,R_m \in Resource$的**仍然需要**资源数。这些进程向量一起构成的矩阵称为Need矩阵，计作$Need_{n \times m}$。易知，对于某一时间片$t$来说，$Need^t_{n \times m}=MAX_{n \times m} - Allocation^t_{n \times m}$

# 算法思路

## 问题一：当前状态是否安全

对于系统对每个线程是否分配资源执行的策略如下：

如果当前进程需要的资源数各个维度都满足当前$\boldsymbol Available$向量的全部维度，就认为这个进程可以执行完毕，即$\exists P_i \in Processor$，$\forall r_{Need} \in Need_{i}$，$\forall r_{Available} \in \boldsymbol {Abailable}$都有$r_{Need} \leq r_{Available}$，则认为当前进程$P_i$可以执行完毕，执行后将当前进程$P_i$的已经分配的资源向量$Allocation_{i}$与可用向量$\boldsymbol  {Available}$相加，作为新的可用向量，并重新检查第一个进程。反之，则认为无法执行跳过当前进程。

当全部进程都进过检查并没有刻意执行完毕的进程，我们认为这个系统时不安全的会发生deadlock。即$\forall P \in Processor$，$CanRun\(P\) \equiv FALSE$

## 问题二：如果某一个时间片$t$某个线程$P_i$请求一个资源向量$\boldsymbol R$，则状态是否安全

主体做法与问题一相同，仍然是对进程进行遍历检查。在什么时刻加入请求资源即对当前时刻的$Alloction_i$加上请求的资源向量$\boldsymbol R$，同时对当前时刻的$\boldsymbol Available$减去请求的资源向量$\boldsymbol R$，所得的结果作为新的Context参与评估。
