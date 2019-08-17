title: Leetcode刷题-190817-汉明重量和位运算技巧
author: Vaskka
tags:
  - 算法
categories:
  - 刷题
date: 2019-08-17 18:57:00
---
# 题目描述

> 题目地址：[位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/)

意思是给一个unsigned int的数，我们需要知道其中二进制表示中1的个数。（也称为**汉明重量**）

看起来很简单的一道题，主要是想记录一下官方题解中关于掩码的思路和如何利用位运算消除整数最后一位1的小技巧。（位运算博主弱项，多记录熟悉一下。）

# 思路

在介绍上面提到官方题解中的那两个小技巧之前，先说明一下java中unsigned int和其他平台的不同。在c/cpp中我们有uint32_t这个类型，在posix标准中，它有4个byte长。一般定义在inttypes.h中，是unsigned int由typedef定义而来。*（typedef可以帮助c/cpp解决不同平台中诸如字长不同而引起的问题）*。由于本身就是无符号的，表示数使用的原码*（毕竟无符号数补码和原码相同，我们理解为原码）*。而java不同，没有诸如unsigned这种修饰符，一般使用long代替，而这道题利用负数按传统的补码进行存储的特点实现。因此对于这道题，想要输入11111111111111111111111111111101，在java平台我们就需要使用-3代替，相应在算法逻辑中就要注意。而这点在第一次做题的时候没有意识到，导致wa了很久......

正式介绍这道题的解法，我们的思路就是遍历每一位（0-31），是1就记录自增。常规检查每一位是不是1我们使用原数与掩码做按位与运算，检查结果是否为0。掩码是利用与操作的特点(0 & n == 0, 1 & 1 == 1)，将相应的原码与掩码进行按位与，我们构造特定的掩码即可使得结果成为我们想要的样子。对于这道题，我们检查每一位的用的掩码就是在这一位设置为1，其他位设置为0（诸如1，10，100，1000）。这样如果第n位是1，那么结果不是0；相反，第n位是0，那么结果也将是0。以下是实现：

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
    	// 用于计数 
        int res = 0;
        
        // 初始掩码mask为1，每次向左移动1位，效果为 1->10->100->...
        for (int i = 0, mask = 1; i < 32; i++, mask <<= 1) {
            if ((n & mask) != 0) {
                res++;
            }
        
        }
        
        return res;
    }

}
```

接着是记录一下题解中关于如何利用 n & (n - 1) 来消除最后一个1位。在二进制表示中n-1一定会将中最后一位1借走去填满比这位低的剩余位。可能有点绕，结合下面这张图（来源：位1个数官方题解）我们能更清晰的理解：

![n&(n-1)图解](https://pic.leetcode-cn.com/abfd6109e7482d70d20cb8fc1d632f90eacf1b5e89dfecb2e523da1bcb562f66-image.png)

利用这个小技巧，我们就能一次次的消除1并递增计数器，虽然和上面解法都是O(1)，但是这会节约判断0的时间。

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int res = 0;
        
        // 注意在java中，会存在负数表示第一位为0的无符号整数的情况，因此这里是!=0而不是>0，相反在c/cpp中我们可以使用>0
        while (n != 0) {
            res++;
            n &= (n - 1);
        }
        
        return res;
    }

}
```

# 小结

我觉得n & (n - 1)的思路真的很巧妙，真的不知道原题解是怎么想到的，可能是经验吧。新手村总是提升特别快，毕竟最初什么都不知道，过去判断某位是不是1我还要理解一会儿为什么要用按位与实现...现在想起来之前有点憨憨啊2333。

# 参考

+ [c/c++: uint8_t uint16_t uint32_t uint64_t size_t ssize_t数据类型 cnblogs 爱你一万年123 web 2019-08-17](https://www.cnblogs.com/ainima/p/6331157.html)
+ [C++入门教程（十七）：二进制和正负数 CSDN 小古银 web 2019-08-17](https://blog.csdn.net/xiaoguyin_/article/details/79777842)
+ [Java 中处理 unsigned int CSDN zhbpd web 2019-08-17](https://blog.csdn.net/zhbpd/article/details/80050481)