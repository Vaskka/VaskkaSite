title: OS-6
author: Vaskka
tags:
  - OS
  - note
categories:
  - notes
date: 2019-06-05 13:51:00
---
# File Management

> 帮助管理外存

## 特点

+ 长期存在
+ 进程共享
+ 结构化存储

## 文件操作

增删开关读写

是一个抽象概念

提供一层抽象接口，具体实现由操作系统管理。

> 虚拟内存也是由操作系统抽象，概念相似。

## 术语

### 域

> Field

基本数据单元

### 记录

相关域的集合

Example: employee record

### 文件

相似record的集合

### 数据库

文件的集合

## 功能

+ 访问权限
+ 目录管理
+ 并发
+ 用户quote
+ 审计

### 实现

+ 标示、定位文件
+ 文件块化
+ 性能优化
+ 管理空闲块

## 文件的组织方式

### 评价标准

+ 短的存取时间
+ 易修改
+ 维护简单可靠

### 实现方式

#### 堆文件

> The Heap/Serial/Pile files

+ 按照时间方式直接存储
+ 无结构
+ access by exhaustive (穷举)

**特点：** 

+ 比较任意性，直接放record 
+ 久的删掉，会产生很多外部碎片

#### 顺序文件

> Sequential File

**特点：**

+ 每条record长度相同

##### 新增

放入外部，通过指针索引此存储区域。

存在Overflow File 存储溢出文件，一定时间后进行整理。

#### 索引顺序文件

> 用索引Key做一个index

+ 有一个Field做Key Field，按照key进行排序
+ 有指针连在一起
+ 可以多级索引提速

同样存在Overflow File

#### 索引文件

使用多个索引

##### 两种不同的索引方式

+ 完全索引，一个一一对应的索引
+ 部分索引

##### Advantage and Disadvantage

速度快但是需要额外的存储储存索引

#### 散列文件

类似Index File，也存在Overflow File

## 文件目录

> 一个目录本身是一个文件

### 层次树状目录

> 主流实现

设计模式：**合成模式**

![合成模式](https://raw.githubusercontent.com/Vaskka/GitLearn/master/lab15/%E5%90%88%E6%88%90%E6%A8%A1%E5%BC%8F.png)

## 文件共享

### 权限

+ None 无，完全无法意识到那里有文件（不可见）
+ Knowledge 知道（可见）
+ Execute 执行
+ Read
+ write
+ Appending 追加（不能删除） 
+ updating 更新
+ Changing protection 更改保护
+ Deleting 删除

### 访问授权

+ Owners 所有者

### 同时访问

> 同步互斥-读写问题

## Record Blocking

> 记录分块

+ 固定组块
+ 可变长度的非跨越式组块

## 文件分配

+ 怎么分配
+ 空间管理

### 预分配

把文件所需要的全部空间一次性分配

#### 缺陷

典型缺陷：日志文件

### 动态分配

每次分配一部分，在空间不足再分配新的。

记录的手段：FAT（File Allocation Table）查找文件都用了哪些块。

### 分配的手段

#### 连续分配

> 预分配的策略

文件创建时分配一组连续的块。

缺陷：为保证空间会存在碎片，需要定时进行整理工作。

#### 链式分配

每一个块抽象为链表中的节点。

#### 索引分配

每个文件在FAT中存在一个索引。

+ index block：包含存储块的指针。

真正占据的块数 = 索引块 + 文件content块

## 空闲空间管理

+ 位表
+ 链式空闲区
+ 空闲索引表
+ 空闲列表（放入硬盘上，管理硬盘上的空闲块）