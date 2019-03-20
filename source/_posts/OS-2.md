title: OS-2
author: Vaskka
tags:
  - note
  - os
categories:
  - notes
date: 2019-03-20 13:51:00
---
# 进程控制

+ 用户模式
	+ 很少的被允许的指令
    + 应用经常运行在这个模式下
+ 系统模式、控制模式、内核模式
	+ 可以访问到系统和内核的指令
+ 两种模式的转换
	+ user->system

## 创建进程

+ 分配id
+ 分配空间
+ 初始化控制块
+ ...

> init pid = 1
全部进程的祖先进程

## 切换进程

$p_0$ -> save into $PCB_0$ -> reload $PCB_1$ -> do $p_1$ -> save $PCB_1$ -> reload $PCB_0$ -> do $P_0$ -> ...

### User Mode -> Kernel Mode

#### 原因

+ 中断
	+ 时钟中断
    + I/O中断
+ 异常
+ 系统调用（Supervisor Call）

#### 细节

+ 保存COntext
+ load PCB
+ into queue
+ 调度程序选择合适进程执行（优先级or something）
+ 改变相应进程状态
+ 更改数据结构
+ 恢复

## 操作系统的执行进程的策略

### 无进程内核

全部任务在kernel中进行

### 用户进程执行

对每个进程进行内存分配。

将应用程序需要的内存空间分配到每个进程中。

进程空间：
+ 用户空间
+ 内核空间
	+ 操作系统的状态备份

空间分配方案：
+ PCB
+ Stack
+ private User
+ ...

### 基于进程的操作系统

> 适用于多核的处理器

每个操作系统将自身当作一个进程，交互数据通过switch function进行。

## Unix SVR4 进程管理模型

采用用户进程的模式

### Unix 进程状态

+ 用户模式运行
+ 内核模式运行
+ 就绪态
+ 阻塞
+ 挂起就绪态
+ 挂起阻塞态
+ 抢占
+ 初始态
+ 僵尸态

## Linux 进程管理与系统调用

### 创建进程-fork()

```c
#include <sys/types.h>
#include <unistd.h>
pid_t fork(void);
```

调用fork() 后子进程和父进程共享代码。

### 退出进程

exit(int status) : 退出一个进程
atexit(void) : 退出进程前可以执行一个函数
wait(int * status) : 等待子进程结束
waitpid(pid_t pid, int *status, int option) : 等待某个进程号的子进程结束

# 进程

## 进程与线程的相似性

+ 动态性
+ 并发性

## 多线程

MS-DOS: 单线程、单进程

+ 资源分配的最小单位——进程
+ 调度的最小单位——线程

线程——创建在进程的地址空间（成本低）


## 线程的特点

### 状态

+ 派生其他线程
+ 阻塞
+ 结束阻塞
+ 终止线程

### 线程同步

### 线程类型

+ 用户级别线程
+ 内核级别线程

#### 用户级别线程

切换成本小
线程被阻塞，整个进程被阻塞。

#### 内核级线程

成本高

#### 混合式

both of them

### Linux 线程管理

#### 创建

```c
#include <pthread.h>
int pthread_create(pthread_t * restrict tidp,
	...);
```


