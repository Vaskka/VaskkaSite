title: OS-4
author: Vaskka
tags:
  - note
  - os
categories:
  - notes
date: 2019-05-08 13:57:00
---
# 虚拟内存

每个进程独占整个**虚拟内存**空间

## 包含

+ 0x000-0x400
+ 用户代码
+ 未分配的.bss数据
+ 已经初始化的.data数据（全局变量）
+ 运行时heap（malloc分配的）
+ 栈
+ 内核代码（内核虚拟内存）
+ 物理内存（内核虚拟内存）

## 大小

与操作系统位数有关，$2^{32}$或者$2^{64}$, like this.

> pagefile.sys: 虚拟内存再硬盘上的存在形式。

## 缺页中断

> 当需要的地址不在main memory时。

## solve

### 进程分块

主存中保留对个进程。当 不需要某个块的时候，move out，需要的时候move in。

### 只使用main memory

应用面窄，只能使用再单片机等单任务的processor。

## 地址翻译

如何再虚拟地址和物理地址上进行映射。

### locality 

> 局部性原理

#### Temporal Locality

> 时间局部性

刚被访问的数据会很快的再访问。

#### Spatial Locality

> 空间局部性

临近的地址优先被选择。

#### 硬件支持

基地址存放在寄存器上。

#### 软件支持

使用什么算法使得段上数据再main memory上高效移动。

### Page Table

+ 虚拟内存把主存当作cache使用。
+ 物理内存分的块叫帧，虚拟内存分的块叫页。
+ size of page: 1k-8k

页表将页的编号映射到主存上。

每个页表项包含：
+ caching disabled
+ referenced（利用局部性）
+ modified（标记页面换出时，是否经历过修改，有就更新main memory）
+ protection（只读只写等）
+ present/absent（是否命中，为命中改进程状态suspend，引发page fault）
+ page frame number（映射到物理内存的地址）

> 二级页表解决一级页表过大问题。

> 二级页表的number储存一级页表的基地址。

### 反向页表

物理->虚拟（必须是全局的）

#### 实现

hash table

### TLB

> 对相关的虚拟物理内存进行cache

### page size

页面小，内部碎片越少，进程中页多。

### 页段

> 每个段大小不等，动态的大小。

### 段表

+ 内存段
+ 每个entry包含段长，段基址，段属性。

### Linux

+ 内核有代码段，数据段
+ 用户有用户代码段，数据段
+ N task 段
+ ...

### 