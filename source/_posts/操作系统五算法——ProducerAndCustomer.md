title: 操作系统五算法——ProducerAndCustomer
author: Vaskka
tags:
  - OS
categories:
  - 期末复习
date: 2019-06-12 15:26:00
---
> 利用复习这段时间正好扩充一下博客，准备在接下来好好总结一下操作系统（软件工程专业）这门课必考的5类算法，分别是**生产者消费者问题**、**死锁预防算法Banker**、**Page Fault的处理换页算法**、**进程调度算法**、**I/O访问和磁盘调度算法**

# 生产者消费者问题

> 生产者消费者模型一直是同步问题中最经典的模型之一。

## 问题描述

消费者，生产者**同时对一个缓冲区变量**进行操作。每次生产者**生产**一个，每次消费者**消费**一个。当**缓冲区为空**时消费者不能再消费，当**缓冲区满了**生产者不能再生产。

## 思路

对于生产者和消费者的互斥操作来说，可以用一个条件变量解决。对于为空时消费者不能消费的条件使用一个信号量标记控制是否为空，且每次生产者进行生产时通知消费者进行消费，不能消费便阻塞。同样，对于缓冲区满了生产者不能再生产，也用一个条件变量控制思路相似此处不在赘述。

每次生产者流程：
![生产者流程](https://raw.githubusercontent.com/Vaskka/GitLearn/master/OS-5-algorithm/producter-and-customer/ProductorAndCustomer-Producer.png)
每次消费者流程：
![消费者流程](https://raw.githubusercontent.com/Vaskka/GitLearn/master/OS-5-algorithm/producter-and-customer/ProductorAndCustomer-Customer.png)

## 实现Demo

> 生产者消费者多个线程实现

main.cpp
```cpp
//
//  main.cpp
//  producer_and_customer
//  working on x86_64-apple-darwin18.6.0 and complied by clang-1001.0.46.4
//  Created by 曹志铭 on 2019/6/12.
//  Copyright © 2019 Vaskka. All rights reserved.
//

#include <iostream>
#include <pthread.h>
#include <unistd.h>

#define MAX_BUFFER_NUM 10

// 生产的标记
#define PRODUCER_FLAG 2333
// 消费的标记
#define CUSTOMER_FLAG -1
// 初始化标记
#define INIT_FLAG 0

// 生产者线程数量
#define PRODUCTER_NUM 10
// 消费者线程数量
#define CUSTOMER_NUM 1

/* 全局功共享变量，包括主要讨论的缓冲区变量和一些条件变量 */

// 互斥锁
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;


// 缓冲区满的条件变量
pthread_cond_t full = PTHREAD_COND_INITIALIZER;


// 缓冲区空的条件变量
pthread_cond_t empty = PTHREAD_COND_INITIALIZER;


// 缓冲区，demo以int类型为例
int * buffer = new int[MAX_BUFFER_NUM];

// producer 生产的当前位置
int producerIndex = 0;

// customer 消费的当前位置
int customerIndex = 0;

// 产品个数
int count = 0;

/**
 * 生产者处理函数
 */
void * producer(void * pid) {
    
    // unbox producer id
    int producerId = *((int *) pid);

    while (true) {
        // 进入临界区
        pthread_mutex_lock(&mutex);
        

        // 等待可以生产（等待缓冲区非满）
        while (count) {
            pthread_cond_wait(&full, &mutex);
        }
        
        // 进行生产
        buffer[producerIndex] = PRODUCER_FLAG;
        
        // 统计变量j自增
        count++;
        
        // print
        std::cout << "生产者" << producerId << "，在"
        << producerIndex
        << "位置生产了一个，"
        << "当前产品有" << count << "个。" << std::endl;
        
        // 环形自增
        producerIndex = (producerIndex + 1) % MAX_BUFFER_NUM;
        
        // 减慢运行速度，方便看到过程
        sleep(0.5);
    
        // 通知消费者缓冲区已经不空了，可以消费了
        pthread_cond_signal(&empty);
        
        // 出临界区
        pthread_mutex_unlock(&mutex);
    }
    
}


/**
 * 消费者处理函数
 */
void * customer(void * cid) {
    
    // unbox customer id
    int customerId = *((int * ) cid);
    
    while (true) {
        // 进入临界区
        pthread_mutex_lock(&mutex);

        
        // 等待可以消费（等待缓冲区不空）
        while (!count) {
             pthread_cond_wait(&empty, &mutex);
        }
       
        // 进行消费
        buffer[customerIndex] = CUSTOMER_FLAG;
        
        // 更新总计变量
        count--;
        
        // print
        std::cout << "消费者" << customerId << "，在"
        << customerIndex
        << "位置消费一个，"
        << "当前产品有"
        << count
        << "个。" << std::endl;
        
        // 环形自增
        customerIndex = (customerIndex + 1) % MAX_BUFFER_NUM;
        
        // 减慢运行速度，方便看到过程
        sleep(0.5);
        
        // 通知生产者可以消费了（解除满的状态）
        pthread_cond_signal(&full);
        
        // 出临界区
        pthread_mutex_unlock(&mutex);
    }
 
}


int main(int argc, const char * argv[]) {
    // 初始化buffer
    for (int i = 0; i < MAX_BUFFER_NUM; i++) {
        buffer[i] = INIT_FLAG;
    }
    
    
    // 初始化线程
    pthread_t producerThreads[PRODUCTER_NUM];
    pthread_t customerThreads[CUSTOMER_NUM];
    
    // 创建生产者线程
    for (int i = 0; i < PRODUCTER_NUM; i++) {
        // box当前producer id
        pthread_create(&producerThreads[i], NULL, producer, (void *) (&i));
    }
    
    // 创建消费者线程
    for (int i = 0; i < PRODUCTER_NUM; i++) {
        // box当前custom id
        pthread_create(&customerThreads[i], NULL, customer, (void *) (&i));
    }

    
    // 等待运行完毕
    for (int i = 0; i < PRODUCTER_NUM; i++) {
        pthread_join(producerThreads[i], NULL);
    }
    
    for (int i = 0; i < CUSTOMER_NUM; i++) {
        pthread_join(customerThreads[i], NULL);
    }

    return 0;
}

```
输出：
![demo2运行结果](https://raw.githubusercontent.com/Vaskka/GitLearn/master/OS-5-algorithm/producter-and-customer/demo2-output-fix.png)
从输出来看，实现了生产者消费者的同步互斥问题。

# 一些补充

在demo实现中我们可以看出，对于生产者和消费者的临界区处理我是先加锁再pthread_cond_wait，而思路中我们计划的是先wait再加锁。这里的原因如下，思路中的wait计划使用的是类似原语的操作，即具有原子性；而pthread库提供的wait接口int pthread_cond_wait(pthread_cond_t* cond, pthread_mutex_t* mutex);的实现过程为：
+ 先将mutex解开
+ 再操作cond
+ 再把mutex锁回去

因此，想要让这一操作也是原子性的，我们需要把临界区的范围扩大即提前加锁。当然了，signal与unlock的操作与思路中提到的顺序相反同样是这个原因。

# 参考

[CSDN-liushall-【操作系统】生产者消费者问题](https://blog.csdn.net/liushall/article/details/81569609)