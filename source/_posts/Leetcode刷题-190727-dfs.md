title: Leetcode刷题-190728-dfs-HashSet与hashcode()的原理
author: Vaskka
tags:
  - 算法
categories:
  - 刷题
date: 2019-07-27 22:03:00
---
# 题目描述

> 题目地址 [递增子序列](https://leetcode-cn.com/problems/increasing-subsequences)

题目意思大概是说给一个随机的数列要输出其中全部不重复的递增子数列且长度不能小于2。直观来看应该是一个dfs没什么好说的，记录这道的原因是因为中间因为不熟悉java的hashcode和HashSet的设计原理导致出现了wa。当时情况是输出了很多很乱，预期也是很多很乱，导致我以为是思路有问题看了好久......后来比较两个输出发现相差的两个数列没什么相关性，于是想到是不是我用的HashSet那里的问题，更正之后ak了.....

于是为了不挖坑，这里赶紧学习一下java的HashSet容器和Object::hashcode()的原理。


# 实现

> 怎么解题的就不多啰嗦了，直接po实现，利用的HashSet去重复，很清晰的dfs。

```java
public class Solution {

    private List<List<Integer>> result = new ArrayList<>();
	
    // 去重复HashSet
    private Set<List<Integer>> set = new HashSet<>();

    void process(int[] nums, int index, List<Integer> curr) {

        curr.add(nums[index]);

        if (curr.size() >= 2) {
			
            // 去重复后加入结果
            if (!set.contains(curr)) {
                set.add(curr);
                result.add(new ArrayList<>(curr));
            }
        }

		// 正常dfs
        for (int i = index + 1; i < nums.length; i++) {
            if (nums[i] >= nums[index]) {
                process(nums, i, new ArrayList<>(curr));
            }
        }

    }

    public List<List<Integer>> findSubsequences(int[] nums) {

        for (int i = 0; i < nums.length; i++) {
            process(nums, i, new ArrayList<>());
        }

        return result;
    }
}
```

时间和空间都不是很理想，不过这不是这篇文章的重点，重点是学习记录一下java中HashSet的实现和Object::hashcode()的原理。

# HashSet与hashcode()


## Object::hashcode() 的原理

根据Object.java 对于public native int hashCode();的文档来看，hashcode这个方法的设计有这样几个特点：

1. 两次java程序的执行中，对于同一个对象调用hashcode方法返回的整数一般是不同的，但是同一个java程序中对同一个对象调用hashcode方法返回的整数是相同的。这一点在第3点中有更详细的解释。
2. 如果两个对象调用equals方法返回true那么这两个方法的hashcode一定是相等的，但是相反如果为false，那么这两个方法的hashcode不一定相等。但是如果我们在设计时使得这两个hashcode能够不同会提高哈希表（java.util.HashMap）的效率。
3. java中对与这一接口的设计我们看到是个native的接口类型，也就是说实现并不是java语言定义的，而是由具体的native库进行实现。在文档中也提到，一般采用jvm为对象分配的抽象地址作为hashcode但并不绝对。（非java语言定义）

我看到这几条定义第一反应是equals返回true的两个对象真的hashcode也一定相同吗？于是做了一个实验，很简单让一个类的equals方法恒返回true，结果是equals会返回true，而每个对象的hashcode各不相同。那么我们也许可以对上面的几条定义看作是java对Object类hashcode方法和equals方法的约定。

查阅了一些资料，jvm会生成一个hashtable，每个Object都会在这个HashTable上面记录，相同的hashcode的Object会形成链表记录在同一个Key下面，那么这时候要想确定两个对象是否相等就只有equals方法才能确定，因为hashcode只能确定这两个对象是不是在同一个Key下的链表中。

## HashSet的原理

首先看一下HashSet的定义：是Set的一种**无序**，**不可重复的**，**允许存放null**的容器。

HashSet的ADT：

```java
 public int size();
 
 public boolean isEmpty();
 
 public boolean contains(Object o);
 
 public boolean add(E e);
 
 public boolean remove(Object o);
 
 public void clear();
 
 public Spliterator<E> spliterator();
```

看过HashSet的实现后发现其实内部是使用HashMap模拟的，利用HashMap中Key唯一的特点，并将Value填充一个final Object对象。

那么这其中很多接口就很明了了，本质上是对HashMap的操作。再去翻阅HashMap的实现，我们发现HashMap对于hash的操作是这样的：
```java
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```
也就是说相同HashMap::hash方法返回不同结果的方法是令key.hashcode()返回不同的值。

最后一个方法是jdk8中对多线程优化的方法，查一些资料明白了这个方法会返回查一些资料明白了这个方法会返回Spliterator，可以对容器进行多线程的遍历，切成几片每片分给一个线程执行。这个坑之后详细理解一下。

## 小结

回到我们这道题，之前出现和正确答案相差两个不相关的数列的原因也就很明了了。在设计hashcode的时候出现了hashcode的碰撞导致没有加入结果集中，在后期我也试过了几种不同的hash的实现但是都发生了碰撞（最初的set内部是Integer，所以一直在尝试尽量不碰撞的hash，思路偏了），最后用回java定义的hashcode才过了用例。

为了搞明白为什么错读了一点点相关的源码，发现自己还是懂的太少太少了。一直觉得自己工程在同龄人还算合格，但是越了解越发现自己的渺小。经常说着自己用着jdk8，然而直到今天才知道Spliterator这东西。对于HashMap的源码也只是简单理解过一遍，在日后读研什么的尘埃落定后，一定好好填坑。路真的很长。

## 参考

[java集合之HashSet](https://www.cnblogs.com/zwbg/p/5906542.html)

[hashcode的作用](https://blog.csdn.net/chinayuan/article/details/3345559)

[jdk8中Spliterator的作用](https://www.cnblogs.com/nevermorewang/p/9368431.html)