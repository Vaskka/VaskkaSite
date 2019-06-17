title: 操作系统五算法——PageFault置换
author: Vaskka
tags:
  - OS
categories:
  - 期末复习
date: 2019-06-17 19:44:00
---
> 主要介绍三种主要的置换策略，Optimal、LRU、Clock with userBit，由于较为简单，故以一道例题做演示，不做过多解释。

# 例题描述

进程的frame访问顺序：
7 0 1 2 0 3 0 4 2 3 0 3 2 1 2 0 1 7 0 1

# Optimal

OPT算法假设我们清楚的知道进程接下来每次访问的次序和具体的frame。（理想状态）

![OPT](https://raw.githubusercontent.com/Vaskka/GitLearn/master/OS-5-algorithm/PageFault/opt.jpg)

# LRU

最近未被使用，针对OPT理想状态的近似。

![LRU](https://raw.githubusercontent.com/Vaskka/GitLearn/master/OS-5-algorithm/PageFault/LRU.jpg)

# FIFO

先进先出。

![FIFO](https://raw.githubusercontent.com/Vaskka/GitLearn/master/OS-5-algorithm/PageFault/FIFO.jpg)

# Clock with useBit

对每个frame增加一个userBit用来记录是否使用过，每次插入一个frame都把这个frame的useBit设置为1，所有的frame形成一个环形链表。缺页时循遍历这个环形链表。存在两种情况：
+ 检查当前的frame的useBit为0，已经找到合适的牺牲页，进行替换并标记useBit为1。
+ 检查当前的frame的useBit为1，未找到合适的牺牲页，把当前的useBit制为0，并检查当前frame的下一个frame。

![Clock with useBit](https://raw.githubusercontent.com/Vaskka/GitLearn/master/OS-5-algorithm/PageFault/Clock.jpg)