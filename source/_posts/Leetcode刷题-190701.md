title: Leetcode刷题-190701
author: Vaskka
tags:
  - 算法
categories:
  - 刷题
date: 2019-07-01 22:29:00
---
> 19/07/01

# 最佳观光组合

[题目描述](https://leetcode-cn.com/problems/best-sightseeing-pair)

大概意思是：输入一个list其中第i个元素和第j个元素的距离为$abs(i-j)$，他们的评分为$list\[i\] + list\[j\] + abs(i-j)$。现在希望输出list中评分最高的那个分数。

第一思路模拟，很显然$O(n^2)$的复杂度是必定TLE的。
```java
public int solve(int[] A) {
	
    int max = 0;
	
    // O(n^2) bad idea
	for (int i = 0; i < A.length - 1; i++) {
    	for (int j = i + 1; j < A.length; j++) {
        	int current = A[i] + A[j] + Math.abs(i - j);
            if (current > max) {
            	max = current;
            }
        }
    }
	
    return max;
} 

```

后来思路是拆分这个评分公式为$(list\[i\] + i) + (list\[j\] - j)$（假设i > j），我们可以固定第一个括号，只遍历一次，在每次保证这个括号为最大和整个评分为最大。原因是第一个括号和第二个括号是分开的，只要保证不是同一个元素即可，即保证$i \not= j$。实现中看的清晰一些：

```java
public int solve(int[] A) {
	
    int max = 0;
    int firstMax = A[0]; // 精确的表述是A[0] + 0;
	
    // 固定第一个括号每次检查是否把当前元素加到第二个括号中。
	for (int i = 0; i < A.length; i++) {
    	// 检查总评分是否需要更新，此时firstMax hold的是这个元素之前全部元素最大的那个。
        max = Math.max(max, firstMax + A[i] - i);    
        // 检查第一个括号的最大值是否需要更换为当前元素的。
        firstMax = Math.max(firstMax, A[i] + i);
        
        /* 注意这里的两行不能调换，
        如果调换顺序即先更新firstMax就不能保证firstMax hold的是当前元素前面全部元素最大的值（A[x] + x）, obviously. */
        
    }
	
    return max;
```