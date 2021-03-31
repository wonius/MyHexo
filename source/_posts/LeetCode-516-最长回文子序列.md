---
title: LeetCode-516.最长回文子序列
copyright: true
date: 2020-12-21 17:09:58
tags: LeetCode
categories: 算法
keywords:
- 双指针
description: 最长回文子序列
---

# 题目

给定一个字符串 `s` ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 `s` 的最大长度为 `1000` 。

 

**示例 1:**
输入:

```
"bbbab"
```

输出:

```
4
```

一个可能的最长回文子序列为 "bbbb"。

**示例 2:**
输入:

```
"cbbd"
```

输出:

```
2
```

一个可能的最长回文子序列为 "bb"。

 

**提示：**

- `1 <= s.length <= 1000`
- `s` 只包含小写英文字母

# 题解

## Round 1（失败）

思路：



## Round 2（递归）

复用最长回文子串的思路。区别在于回文函数。

时间复杂度O(N^N)，效率并不高。

```java
/*
 * @lc app=leetcode.cn id=516 lang=java
 *
 * [516] 最长回文子序列
 */

// @lc code=start
class Solution {
    public int longestPalindromeSubseq(String s) {

        /**
         * 2. 复用最长回文子串的思路
         * 
         */
        char[] arr = s.toCharArray();
        int res = 0;
        int leftRes = 0;
        int rightRes = 0;

        for (int i = 0; i < arr.length; i++) {
            leftRes = plalindrome(arr, i, i);
            rightRes = plalindrome(arr, i, i+1);
            if(leftRes>res) res = leftRes;
            if(rightRes>res) res = rightRes;
        }

        return res;
    }

    public int plalindrome(char[] arr, int left, int right){

         // 判断越界
        if(left<0 || right>arr.length-1) return 0;
        // 字符相同时，判断是否为同一字符
        if(arr[left] == arr[right]){
            if(left==right) 
                return plalindrome(arr, left-1, right+1) +1;
            else 
                return plalindrome(arr, left-1, right+1) +2;
        } else {
            // 不同时，取最大
            return Math.max(plalindrome(arr, left-1, right), plalindrome(arr, left, right+1));
        }
    }
}
// @lc code=end
```



## Round 3（）