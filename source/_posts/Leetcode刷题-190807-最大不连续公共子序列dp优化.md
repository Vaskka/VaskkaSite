title: Leetcode刷题-190807-最大可不连续公共子序列的dp优化
author: Vaskka
date: 2019-08-07 16:40:21
tags:
---
# 题目描述

> 题目地址：[https://leetcode-cn.com/problems/delete-operation-for-two-strings/submissions/](https://leetcode-cn.com/problems/delete-operation-for-two-strings/submissions/)

原题说的是要找到两个字符串中，每次删去一个任意位置的字符，最少删去多少次能让两个字符串相等。

# 思路与实现

很直观能看到是要找最长子串。不过要注意的是，这里的字串指的是有序可不连续的字符序列。

举个例子：park与spake的最长“字串”指的是pak。

最初的想法没有想到这里的字串包括不连续的，于是上面的用例便没过，之后思路就偏了，集中到怎么删去字符进行比较。想法是为每个原字符串构建一棵树，树的第i层表示删去第i次，之后遍历这两颗树找最长的字串。穷举的思路当然时间爆炸，在长度为8左右的用例就tle了......

后来翻了题解，这道题的正确思路应该是dp，方程：

$result\[i\]\[j\] = \left\{\begin{array}{lr} result\[i-1\]\[j-1\] + 1 & & {one\[i\]=another\[i\]} \\ max(result\[i\]\[j-1\],res\[i-1\]\[j\]) & & {one\[i\] \neq another\[j\]} \end{array} \right.$

方程中$one$，$another$表示输入的两个字符串，$result\[i\]\[j\]$表示$one$前$i$个字符中与$another$前$j$个字符中最长“字串”（可不连续有序子序列）的长度。

对任意时刻令$context = (i, j)$，此时有两种情况，第$i,j$这两个字符相等或不等。相等时结果应该是**上一个**$context$计算后的结果**加1**，即$result\[i - 1\]\[j - 1\]$。

不相等时，此时不会出现新的子串因为当前不相等，所以新的一次计算不会对子串的个数造成影响，因此我们选两边中上一次结果比较大的一边作为这次结果。

直观dp实现：

```java
public class Solution {


    // dp function
    private int result(char[] oneCharArray, int i, char[] anotherCharArray, int j) {

        // i < 0 或者 j < 0 表示某一方缩减到空字符串了，显然无子序列
        if (i < 0 || j < 0) {
            return 0;
        }

        if (oneCharArray[i] == anotherCharArray[j]) {
            return result(oneCharArray, i - 1, anotherCharArray, j - 1) + 1;
        }
        else {
            return Math.max(result(oneCharArray, i - 1, anotherCharArray, j), result(oneCharArray, i, anotherCharArray, j - 1));
        }

    }


    public int minDistance(String word1, String word2) {

        int maxSubSeqLength = result(word1.toCharArray(), word1.length() - 1, word2.toCharArray(), word2.length() - 1);

        // 结果 = 串1 - maxSubSeqLength + 串2 - maxSubSeqLength
        return  word1.length() + word2.length() - 2 * maxSubSeqLength;
    }

}
```

过了26个用例左右超时了（dp日常tle...），使用常规数组优化，具体思路可以结合注释看。实现：

```java
public class Solution {


    // dp function
    private int result(int[][] cache, char[] oneCharArray, int i, char[] anotherCharArray, int j) {

        // i < 0 或者 j < 0 表示某一方缩减到空字符串了，显然无子序列
        if (i < 0 || j < 0) {
            return 0;
        }

        if (oneCharArray[i] == anotherCharArray[j]) {
            // 因为cache[0] 留给空串，所以正常的cache从1开始，字符数组正常从0开始
            if (cache[i][j] != -1) {
                // cache命中
                cache[i + 1][j + 1] = cache[i][j] + 1;
                return cache[i + 1][j + 1];
            }

            // cache未命中
            return result(cache, oneCharArray, i - 1, anotherCharArray, j - 1) + 1;
        }
        else {

            // 同样cache[0]留给空串，所以cache的index都比数组的多1
            int oneSide = cache[i][j + 1] != -1 ?  cache[i][j + 1] : result(cache, oneCharArray, i - 1, anotherCharArray, j);
            int anotherSide = cache[i + 1][j] != -1 ? cache[i + 1][j] : result(cache, oneCharArray, i, anotherCharArray, j - 1);
            
            cache[i + 1][j + 1] = Math.max(oneSide, anotherSide);
            return cache[i + 1][j + 1];
        }

    }


    public int minDistance(String word1, String word2) {

        // 1～length 为正常字符串cache位, 0为空字符串cache位
        int[][] cache = new int[word1.length() + 1][word2.length() + 1];

        // 空串位置赋为0, 其他位置为-1表示未初始化
        for (int i = 1; i < cache.length; i++) {
            for (int j = 1; j < cache[0].length; j++) {
                cache[i][j] = -1;
            }
        }

        // 最长有序子序列的长度
        int maxSubSeqLength = result(cache, word1.toCharArray(), word1.length() - 1, word2.toCharArray(), word2.length() - 1);

        // 结果 = 串1 - maxSubSeqLength + 串2 - maxSubSeqLength
        return  word1.length() + word2.length() - 2 * maxSubSeqLength;
    }


}

```

最后一个是题解中取消递归的实现，时间进一步优化：

```java
class Solution {
    public int minDistance(String word1, String word2) {


        char[] word1cc = word1.toCharArray();
        char[] word2cc = word2.toCharArray();

		// cache
        int[][] publicSeqLength = new int[word1cc.length + 1][word2cc.length + 1];

        for (int i = 1; i <= word1cc.length; i++) {
            for (int j = 1; j <= word2cc.length; j++) {
                if (word1cc[i - 1] == word2cc[j - 1]) {
                    publicSeqLength[i][j] = publicSeqLength[i - 1][j - 1] + 1;
                }
                else {
                    publicSeqLength[i][j] = Math.max(publicSeqLength[i - 1][j], publicSeqLength[i][j - 1]);
                }
            }
        }

        return word1cc.length - publicSeqLength[word1cc.length][word2cc.length] + word2cc.length - publicSeqLength[word1cc.length][word2cc.length];
    }
}

```

# 小结

首先要看出是dp，然后找方程，注意要把每次迭代都需要什么信息考虑清楚，方程列出来之后就都是套路比较好办，重点还是看出是dp而且列对方程。

这几次的练习主要在做dp，但是这道题还是思路偏了，总结起来就是不熟练，不熟悉套路，总之多多练习，目前接触的题还是太少太少了。