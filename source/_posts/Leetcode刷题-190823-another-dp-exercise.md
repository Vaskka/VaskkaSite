title: Leetcode刷题-190823-another dp exercise
author: Vaskka
tags:
  - 算法
categories:
  - 刷题
date: 2019-08-23 14:05:00
---
# 题目描述

[题目地址](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

大概意思是给出一个价格序列，我们需要根据这一价格序列确定一个买卖，使得利润最大。当然买入不可能在卖出之后。

# 思路

首先想到的是模拟，也就是穷举每次卖入和每次卖出找最大，这样做十分容易超时。虽然好理解但是很蠢，这里使用了一个矩阵来模拟买卖的情况。在时间上会优化一些，300多ms很险的过了用例。也记录下来吧。

## 利润矩阵思路

定义利润矩阵$A_{i \times j}$ 为第$i$天卖入，第$j$天卖出时利润的集合。由于我们规定不能在买入前卖出，因此这个矩阵的下三角矩阵每个元素均为0。并且主对角线表示当天买入当天卖出，因此主对角线各元素为0。

有了上面这些性质，我们就可以只计算出上三角矩阵中最大的元素，并且不需要管主对角线。下面是实现：

```java
public int maxProfitN2(int[] prices) {

    int max = 0;

	// i表示第i天买入
    for (int i = 0; i < prices.length; i++) {
        // 因为不需要考虑主对角线，因此j从i + 1开始，表示第j天卖出
        for (int j = i + 1; j < prices.length; j++) {
        
        	// 计算这次的利润
            int t = prices[j] - prices[i];

			// 同时统计出最大值
            if (t > max) {
                max = t;
            }
        }
    }

    return max;
}
```

$O(n^2)$的复杂度，接着看了题解明白这是道dp。又是dp又没有看出来......再次记录一下加深印象。

## dp思路

对于第$i$天的利润存在以下两种情况*（子问题）*：**卖出**or**不卖出**。对于卖出的情况，最大利润即为第$i$天的价格减去前$i-1$天的价格中最小的一个；对于不卖的情况，利润应该为$i-1$天的利润*（可理解为持有商品）*。

理解了思路，那么dp方程也就很明显了：

$R(i)=max(R(i-1), prices\[i\]-min(prices\[0,...,i - 1\]))$

优化前的dp就不贴出来了，是肯定要超时的。下面是传统dp优化与求前n个序列中最小值的$O(n)$优化：

```java

// dp 方程，cache保存dp之前的结果，minCache保存原序列前i个中的最小值
private int process(int[] prices, int i, int[] cache, int[] minCache) {

    int max = 0;

    if (prices.length == 0) {
        return max;
    }

	// 到第一天时，开始回溯
    if (i == 1) {
        return prices[1] - prices[0];
    }

    int origin = cache[i - 1];
	
    // cache未命中需要计算
    if (origin == 0) {
        origin = process(prices, i - 1, cache, minCache);
    }
	
    // 保存当前结果到cache中
    cache[i] = Math.max(origin, prices[i] - minCache[i - 1]);
    return cache[i];
}

public int maxProfit(int[] prices) {

    int max = 0;

	// cache保存dp的子问题结果
    int[] cache = new int[prices.length];
    // minCache保存原序列前i个的最小值
    int[] minCache = new int[prices.length];

	// 一次遍历统计出前i个最小值
    int curr = prices[0];
    for (int i = 0; i < prices.length; i++) {
        if (prices[i] < curr) {
        	// 当前的值比过去的还小说明最小值该易主
            minCache[i] = prices[i];
            
            // 更新最小值
            curr = prices[i];
        }
        else {
        	// 没有最小值小，保留
            minCache[i] = curr;
        }
    }

	// 遍历一遍找利润最大
    for (int i = 1; i < prices.length; i++) {
        int t = process(prices, i, cache, minCache);
        if (t > max) {
            max = t;
        }
    }

    return max;
}

```

# 小结

再次没有看出来是dp题，不过方程倒是列的快了，也算有进步吧，继续加油。