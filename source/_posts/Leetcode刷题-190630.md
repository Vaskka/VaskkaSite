title: Leetcode刷题-190630
author: Vaskka
tags:
  - 算法
categories:
  - 刷题
date: 2019-06-30 15:07:00
---
> 19/06/30

# 猜数字游戏

> [猜数字](https://leetcode-cn.com/problems/bulls-and-cows/submissions)

题目意思大概是两列长度相等的数列，如果第一位称为secret，第二列叫guess，如果对$i \in \{0, ... ,secret.length\}$，有$secret\[i\] = guess\[i\]$称之为公牛，此时的i记录在公牛集$BullSet$中，有$secret\[j\] \in guess \land j \notin BullSet \land j \notin CowSet$，称之为奶牛，此时的j记录在奶牛集$CowSet$中。

简单描述题目基本已经把解法说清楚了，模拟即可，一下是模拟的结果：

```java

    public String getHint(String secret, String guess) {
        char[] secretArray = secret.toCharArray();
        char[] guessArray = guess.toCharArray();
		
        // secret 和 guess 的index可以用作id，故此处是Integer而不是Character
        HashSet<Integer> A = new HashSet<>();
        HashSet<Integer> B = new HashSet<>();

		// 计算公牛
        int i = 0;
        while (i < guessArray.length) {

            if (guessArray[i] == secretArray[i]) {
            	// 记录在公牛集
                A.add(i);
            }
            i++;
        }

		// 计算奶牛
        for (int j = 0; j < guessArray.length; j++) {
        	// 在公牛集中忽略
            if (A.contains(j)) {
                continue;
            }
            // 遍历secret
            for (int k = 0; k < secretArray.length; k++) {
            	// 不在公牛集而且不在奶牛集
                if (!A.contains(k) && !B.contains(k) && guessArray[j] == secretArray[k]) {
               		// 记录在奶牛集
                    B.add(k);
                    break;
                }
            }
        }

        return String.format("%dA%dB", A.size(), B.size());
	}
```

很容易理解但是占内存又大时间又久，下面学习一下一个比较快而且很巧妙的解法，copy from the solution。

```java
    public String getHint(String secret, String guess) {
		
        // ab[0]代表A，ab[1]代表B
        int[] ab = new int[2];
        
        // 数列总长
        int n = secret.length();
		
       	// 当flag，每个数字出现了几次
        int[] cnt = new int[10];
        
        // 计算公牛
        for (int i = 0; i < n; ++i) {
            char s = secret.charAt(i);
            char g = guess.charAt(i);
            
            // 符合公牛条件
            if (s == g) {
                ab[0]++;
                continue;
            }
            
            // 不符合公牛条件但是在secret中出现的数字需要自增，每出现一个就增加1
            cnt[s - '0']++;
        }

        // 计算奶牛
        for (int i = 0; i < n; ++i) {
            char s = secret.charAt(i);
            char g = guess.charAt(i);
            
            // s != g 判断此位不是公牛位（有可能是奶牛位，继续判断），&&后面的表达式cnt[g - '0'] > 0的含义是“而且还在secret中出现了的数字”，只要大于0，意味着至少还剩了1次可以使用，即符合奶牛要求。此处很巧妙，cnt[index]记录了index在secret中出现了多少次，可以作为奶牛位可以分配多少的依据，从而避免了使用HashSet。
            if (s != g && cnt[g - '0'] > 0) {
            	// 消耗了一个奶牛位的位置所以要自减
                cnt[g - '0']--;
                ab[1]++;
            }
        }
        return ab[0] + "A" + ab[1] + "B";
	}
```