title: Leetcode刷题-190713-动态规划
author: Vaskka
tags:
  - 算法
  - 动态规划
categories:
  - 刷题
date: 2019-07-13 15:05:00
---
> 题目地址：[打家劫舍](https://leetcode-cn.com/problems/house-robber-submissions/)

# 题目简介

输入一个数组，数组中每个元素代表一个房子，我们作为robber可以对每个房子进行rob。但是规定不能同时又两个相邻的房子被robbed。数组中每个元素的值代表房子的价值，需要我们输出经过rob后最大的收益是多少。

# 思路

很明显考察的是动态规划，每检查一个房子都有两种状态即**rob**和**no-rob**。对应的价值方程为:

$result(i) = max(rob(i), norob(i))$

$rob(i) = nums\[i\] + result(i - 2)$

$norob(i) = result(i - 1)$

其中，$result(i)$ 表示检查第i个房子最后的收益是多少，它应该为这个房子rob或no-rob这两种情况中较大的那个。$nums$表示输入全部房子信息的数组。可见，如果rob，根据规则我们不能rob两个连续的房子，所以$rob(i)$式子中有$result(i - 2)$这一项，整个方程表示当前这个房子的价值加**向前两个房子计算后的价值**。no-rob比较好理解，什么都不做即为上一个房子计算后的价值。

可能会有不清晰的地方：为什么计算第$i$个房子的价值不需要考虑$i+1$的房子是不是被robbed？其实这里体现动态的一个效果，当检查$i+1$时，过去的$i$即为那时的$i-1$，也就是不必为未来考虑，现在的情况便是未来的过去。只需要保证现在和现在的过去吻合题意即可，因此上面的方程可以解释全部的情况。稍微有一点点绕，不过相信作为读者的你一定能理解这个地方。我做题的时候思考过这个问题，个人感觉还是有点绕的......

# 实现

## 直观模拟

> 根据总结的方程直观模拟

```java
    private static int TLE_Process(int[] nums, int i) {
		
        // 处理空数组
        if (i == -1) {
            return 0;
        }
	
    	// i=0, 只有一种可能即rob
        if (i == 0) {
            return nums[0];
        }
		
        // i=1, nums数组中只有两个，选较大的那个
        if (i == 1) {
            return nums[1] > nums[0] ? nums[1] : nums[0];
        }

        int max = nums[i];

        for (int j = i; j >= 0; j--) {
        	// 考虑到有i-2和i-1所以从后向前遍历
            max = Math.max(nums[i] + TLE_Process(nums, i - 2), TLE_Process(nums, i - 1));
        }

        return max;
	}

	// main
	public int rob(int[] nums) {
    	return TLE_Process(nums, nums.length - 1);
    }

```

很明显这个实现必定tle的，当nums稍大一点较前面的只便会被重复计算很多遍。

## 使用cache时间优化

```java
    // use cache
    private static int cacheProcess(int[] nums, int i, int[] cache) {


        // 在缓存中直接取
        if (cache[i] != -1) {
            return cache[i];
        }

        // 不在cache中，需要算
        int max = nums[i];

        for (int j = i; j >= 0; j--) {
			
            // 仍然是模拟方程，这回使用cache做中间存储
            max = Math.max(nums[i] + cacheProcess(nums, i - 2, cache), cacheProcess(nums, i - 1, cache));
        }
        cache[i] = max;

        return max;

	}


    public int rob(int[] nums) {
		
        // 把nums的特殊情况放在外面处理，避免重复在递归中计算
        if (nums.length == 0) {
            return 0;
        }

        if (nums.length == 1) {
            return nums[0];
        }
		
        // cache中第i个记录着方程中result(i)的值
        int[] cache = new int[nums.length];
        cache[0] = nums[0];
        cache[1] = nums[0] > nums[1] ? nums[0] : nums[1];
        // 没利用的初始化为-1
        for (int i = 2; i < cache.length; i++) {
            cache[i] = -1;
        }

        return cacheProcess(nums, nums.length - 1, cache);
	}
```

这种方式减少了很多的重复计算。

## 进一步压缩空间和时间的cache优化

```java
    // 取消递归 + cache空间优化
    public int robSpaceOptimization(int[] nums) {
        // deal []，处理特殊情况
        if (nums.length == 0) {
            return 0;
        }

        int lastSecond = nums[0];

        // deal [1]，处理特殊情况
        if (nums.length == 1) {
            return lastSecond;
        }

        int lastFirst = nums[0] > nums[1] ? nums[0] : nums[1];

        // deal [1, 2]，处理特殊情况
        if (nums.length == 2) {
            return lastFirst;
        }

        int max = 0;

        // main process 自底向上，使用两个变量代替cache数组尽可能减少cache空间，同时取消递归
        for (int i = 2; i < nums.length; i++) {
        	
            // 此处lastSecond代表result(i-2), lastFirst代表result(i-1)
            max = Math.max(nums[i] + lastSecond, lastFirst);
            
            // 更新result(i-2)和result(i-1)
            lastSecond = lastFirst;
            lastFirst = max;

        }

        return max;
	}
```

通过观察方程我们发现式子中只出现了$result(i-1)$和$result(i-2)$这两个需要重复计算的变量。因此我们没必要使用数组存储全部的过程而只是动态的记录这两个变量的值即可。

## 参考

[思路参考:CSDN HankingHu 算法-动态规划 Dynamic Programming--从菜鸟到老鸟 19/07/13](https://blog.csdn.net/u013309870/article/details/75193592)