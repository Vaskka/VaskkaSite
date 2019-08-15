title: Leetcode刷题-190815-杨辉三角的O(n)优化
author: Vaskka
tags:
  - 算法
categories:
  - 刷题
date: 2019-08-15 14:12:00
---
# 题目描述

> 题目地址：[杨辉三角II](https://leetcode-cn.com/problems/pascals-triangle-ii)

题目要打印第$i$行的杨辉三角，主要记录一下利用二项式定理如何将杨辉三角的通项利用$O\(n\)$的复杂度得到。

# 思路

> 本篇主要记录的是二项式定理的解法，结尾也会把dp的解法和相关优化给出来，博主的dp还不是很熟，记录一下作为练习。

## 二项式定理

对于杨辉三角，我们知道每一行的每一个都是上一行的相邻两数相加，如下图：

![Wikipedia 杨辉三角动态图](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

高中我们知道杨辉三角第$i$行的第$j$个其实就是组合数$C_{i}^{j}$，组合数的通项公式如下:

$C_i^j=\frac{i!}{j!(i-j)!}$

同时还可以推导出递推的形式，这个递推式是优化到$O\(n\)$的关键，递推式如下：

$C_i^j = \frac{i-j+1}{j} \cdot C_i^{j-1}$

同时，

$C_i^0 = 1$

至此，我们利用这个递推式就可以很轻松的实现$O\(n\)$级获得杨辉三角任意一层，实现如下：

```java
public class Solution {
	
    // 这里的rowIndex非负, 小于等于33, 保证long之内有有效结果
    public List<Integer> getRow(int rowIndex) {
    	
        List<Integer> res = new ArrayList<>(rowIndex + 1);
        
        // 使用int范围不够
        long mid = 1;
        res.add(1); 
        
        // i < rowIndex + 1 保证的是计算到最后一个是 C_i^i
        for (int i = 1; i < rowIndex + 1; i++) {
        	// 二项式定理递推式得到每个组合数 
            mid = (rowIndex - i + 1) * mid / i;
            res.add((int)mid);
        }

        return res;
    }

}
```

## dp思路

> 实质上也是二项式定理，不过优化时间后空间会达到$O\(n^2\)$

思路就是模拟杨辉三角的定义*（也许称不上是dp，就是个简单的递推关系，但也使用了将问题化为子问题的思想，我们就理解为是dp吧）*，这里直接给出dp方程：

$r\[i\]\[j\]=\left \{ \begin{array}{lr} 1 & & j=0 \lor j= i \\ r\[i-1\]\[j-1\]+r\[i-1\]\[j\] & & other \end{array} \right.$

下面是dp的实现，分别是直观实现和几种时间优化：

```java
public class Solution {

    // 直观实现
    private int recursiveProcess(int i, int j) {
        if (j == 0 || j == i) {
            return 1;
        }

        return recursiveProcess(i - 1, j - 1) + recursiveProcess(i - 1, j);
    }

    // 时间优化
    private int cacheRecursiveProcess(int[][] cache ,int i, int j) {
        if (j == 0 || j == i) {
            cache[i][j] = 1;
            return 1;
        }

        int one = cache[i - 1][j - 1] == 0 ? cacheRecursiveProcess(cache, i - 1, j - 1) : cache[i - 1][j - 1];
        int another = cache[i - 1][j] == 0 ? cacheRecursiveProcess(cache, i - 1, j) : cache[i - 1][j];

        cache[i][j] = one + another;
        return cache[i][j];
    }
    

    /**
     * 直观dp实现
     * @param rowIndex 行数，非负整数
     * @return List&lt;Integer&gt;
     */
    public List<Integer> getRowRecursive(int rowIndex) {
        List<Integer> res = new ArrayList<>(rowIndex + 1);

        for (int i = 0; i < rowIndex + 1; i++) {
            res.add(recursiveProcess(rowIndex, i));
        }

        return res;
    }

    /**
     * cache优化dp实现
     * @param rowIndex 行数，非负整数
     * @return List&lt;Integer&gt;
     */
    public List<Integer> getRowCacheOptimizeRecursive(int rowIndex) {

        List<Integer> res = new ArrayList<>(rowIndex + 1);
        int[][] cache = new int[rowIndex + 1][rowIndex + 1];

        for (int i = 0; i < rowIndex + 1; i++) {
            res.add(cacheRecursiveProcess(cache, rowIndex, i));
        }

        return res;
    }

    /**
     * cache优化dp以及利用杨辉三角的特点只计算每列的一半
     * @param rowIndex 行数，非负整数
     * @return List&lt;Integer&gt;
     */
    public List<Integer> getRowCacheOptimizeRecursiveAndHalfSameOptimize(int rowIndex) {

        List<Integer> res = new ArrayList<>(rowIndex + 1);
        int[][] cache = new int[rowIndex + 1][rowIndex + 1];

        // 计算前一半
        for (int i = 0; i < rowIndex / 2 + 1; i++) {
            res.add(cacheRecursiveProcess(cache, rowIndex, i));
        }

        // 后一半直接copy，注意rowIndex为奇或偶开始的index不同
        int size = res.size();
        for (int i = (rowIndex % 2 != 0) ? (size - 1) : (size - 2); i >= 0; i--) {
            res.add(res.get(i));
        }

        return res;
    }
}

```

# 小结

二项式定理是高中的内容，忘的是一干二净......最初的思路就是dp，题目要求空间优化到$O\(n\)$，怎么都忘了二项式定理还有个递推式，最后是查了资料才明白。

当然，看了题解发现这道题还可以打表，因为题目给出$0 \leq rowIndex \leq 33$。$O\(1\)$的复杂度，不过起不到什么训练的效果，这里就简单提一句了。

# 参考

+ [组合数公式整理 cnblog henry_y 2019-08-15 web](https://www.cnblogs.com/henry-1202/p/about_combinatorial_number.html)
