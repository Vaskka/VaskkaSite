title: OS-1
author: Vaskka
tags:
  - os
  - note
categories:
  - os
date: 2019-03-06 13:51:00
---
# operating system

## definition

控制应用执行，提供上层应用程序与底层硬件的接口。

## User/Computer interface

> os 需要安装在不同的 instruction set 上，设计实现接口与硬件交互。

os-interface: 向下提供支持，向上提供服务。

二进制接口：为所有应用程序提供接口，提供相应的程序的运行规定。

> e.g. long 长整型，定义在二进制接口中。
e.g. 对齐

### 关键接口

+ ISA：指令集结构
+ ABI：二进制接口，库与操作系统level
+ API：应用程序接口

### 优化与设计

> 模块化，易于扩展和维护。


## 演变与发展

### Serical Processing

ENIAC 1940s-1950s，控制通过硬连接。

冯.诺伊曼，进行改进。

### simple batch system

将一些常用的操作整理成batch运行

> e.g. IBM 7094 简单/单任务批处理系统

特有的处理指令：JCL

后面加入更多的扩展

+ 内存保护
+ timer （避免某个人物独占系统过久，任务调度）
+ cpu mode
   + 用户模式
   + 系统模式
  
### mutliprogramming batch system

> 多道批处理系统

#### 对比：

简单批处理系统，一次只能处理一个任务。（忙等待）

### Time-Sharing System

> 能够更好的利用计算机资源，1960s。

将时间分为time slice，能够从一个工作快速切换到另一个工作。

#### 与多道的批处理的区别

+ 响应时间快很多
+ cpu利用率降低（切换操作消耗）

## Achievement

### 进程 processes

> 运行的实例（正在执行的状态）

#### 组成

+ 可运行的程序
+ 一系列的数据
+ 执行的Context（当前执行的状态）

> 指令：代码段
数据：数据段
Context: 内核或Stack

### 内存管理

#### basis work

+ Process isolation 进程隔离
+ Automatic allocation and management 自动化分配管理内存
	+ first fit
    + best fit
    + ...(os did)
+ Support of modular programming （模块化程序设计）
+ Protection and access control （保存和存取控制）
+ Long-trem storage （长期存储）

#### VM 

> Virtual Memory

process -> MMU(Memory Mapping Unit) -> ...

##### paging

> 进程被分成一个个等大的存储块，成为page

虚拟地址是由一个**page number**和**offset**组成。

### 信息保护和安全

+ 有效
+ 保密
+ 数据完整性
+ 认证

### 调度与资源管理

+ 公平性，使得优先级相似的进程访问资源的公平性。
+ 有差别响应
+ 高效性，单位时间里完成工作的量最大

### 系统结构

+ 模块化
+ 微内核
+ 多线程
+ 对称多进程（Symmetric multiprocessing）