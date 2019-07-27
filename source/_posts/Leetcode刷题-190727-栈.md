title: Leetcode刷题-190727-栈
author: Vaskka
tags:
  - 算法
categories:
  - 刷题
date: 2019-07-27 10:00:00
---
# 题目描述

> 原题地址: [删除字符串中的所有相邻重复项](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string)

大概意思是说在字符串中删除相邻相同的字符，直到没有可以删的为止。

巨明显的栈思路然而我第一次竟然是模拟的......太缺乏训练了，要加油了...

## 实现

> 第一次是傻乎乎的模拟，用的空间是很少但是这思路不是正统做法。不管怎么说还是放出来吧...

```java

	// 找到下一个不是null的有效字符进行比较并操作
    private boolean checkNext(Character[] list, int i) {

        int oindex = i;
        Character origin = list[i];
        Character next = list[++i];

        if (origin == null) {
            return false;
        }

        while (next == null) {
            i = i + 1;
            if (i >= list.length) {
                return false;
            }
            next = list[i];
        }

		// 找到有效字符并比较
        if (origin.equals(next)) {
        
        	// 重复就赋null
            list[oindex] = null;
            list[i] = null;
            return true;
        }

        return false;
    }

    public String removeDuplicates(String S) {

        Character[] list = new Character[S.length()];

        for (int i = 0; i < list.length; i++) {
            list[i] = S.charAt(i);
        }

        boolean redo = true;
        while (redo) {
            redo = false;
            for (int i = 0; i < list.length - 1; i++) {
                if (checkNext(list, i)) {
                    redo = true;
                }
            }
        }
		
       	// 全部筛选完毕利用StringBuilder构造结果
        StringBuilder sb = new StringBuilder();

        for (Character c : list) {
            sb.append(c == null ? "" : c);
        }

        return sb.toString();
    }
```

想法太工程了...真的要加油了，真的太菜了...

看了一看题解瞬间明白这是一道考栈的题。虽然用时比模拟长而且空间也多，但是正统做法会节省很多时间。That's important!

```java
    public String removeDuplicates(String S) {

        Stack<Character>  stack = new Stack<>();

        char[] ss = S.toCharArray();

        for (int i = 0; i < ss.length; i++) {
            if (stack.empty()) {
                stack.push(ss[i]);
            }
            else {
                if (stack.peek() == ss[i]) {
                    stack.pop();
                }
                else {
                    stack.push(ss[i]);
                }
            }
        }

        StringBuilder sb = new StringBuilder();
        for (Character c : stack) {
            sb.append(c);
        }

        return sb.toString();
    }
```

正统做法思路超清晰不过多介绍，写下这篇博客提醒自己不要再连这种低级问题也看不出来了。