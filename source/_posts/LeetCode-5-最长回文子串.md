---
title: LeetCode-5.最长回文子串
copyright: true
date: 2020-12-17 15:11:57
tags: LeetCode
categories: 算法
keywords:
- 双指针
description: 最长回文子串（不是子序列）
---

# 题目

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

**示例 1：**

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

**示例 2：**

```
输入: "cbbd"
输出: "bb"
```

# 题解

## Round 1（常规）

常规解法，基本没用到算法。

```java
/*
 * @lc app=leetcode.cn id=5 lang=java
 *
 * [5] 最长回文子串
 */

// @lc code=start
class Solution {
    public String longestPalindrome(String s) {

        if(s.length()<=1) return s;
        char[] arr = s.toCharArray();
        String resString = "";
        
        // 遍历每个字符
        for(int i=1; i<arr.length; i++) {
            String currString = "";
            String currString2 = "";

            // 假设当前字符左右两两回文，如abcba
            // 保存当前字符
            currString+=arr[i];
            for (int j=0; j < i; j++) {
                // 判断左右两边字符
                if(i+1+j<arr.length && i-1-j>=0 && arr[i+1+j]==arr[i-1-j]){
                    // 不要使用StringBuilder的append，不然你会哭的
                    // 拼接左、中、右字符
                    currString=arr[i+1+j]+currString+arr[i-1-j];
                } else {
                    // 如果不连续，跳出
                    break;
                }
            }

            // 假设当前字符与前面一个字符两两回文，如 abba
            for (int j=0; j < i; j++) {
                // 
                if(i+j<arr.length && i-1-j>=0 && arr[i+j]==arr[i-1-j]){
                    // 拼接左、中、右字符
                    currString2=arr[i+j]+currString2+arr[i-1-j];
                } else {
                    // 如果不连续，跳出
                    break;
                }
            }
            
            // 找极大值
            if(currString.length()>resString.length()) {
                resString = currString;
            }
            if(currString2.length()>resString.length()) {
                resString = currString2;
            }
        }
        return resString;
    }
}
// @lc code=end
```

中间遇到2个坑：

1. 刚开始，currString使用StringBuilder，会发现很多重复元素。原因是append()都是在原对象上操作的；
2. 刚开始打算把2种情况合并到一个for循环中，调了好久发现不行，二者对的结果会相互影响。比如不连续时要跳出循环；

## Round 2（双指针）

上面解法的着力点在单个字符，循环时以某一个字符为主，每次只移动1个字符。

还有一种解法是双指针，每次同时移动2个字符。这也是回文题的特点。

重点关注回文函数中的边界，以及最终如何截取子串。

```java
/*
 * @lc app=leetcode.cn id=5 lang=java
 *
 * [5] 最长回文子串
 */

// @lc code=start
class Solution {
    public String longestPalindrome(String s) {

        /**
         * 双指针，left、right2个指针同时移动
         */
        String resString = "";

        for (int i = 0; i < s.length(); i++) {
            // 左右指针在同一位置
            String leftRes = plalindrome(s, i, i);
            // right指针在left指针后
            String rightRes = plalindrome(s, i, i+1);
            
            if(leftRes.length()>resString.length()) {
                resString = leftRes;
            }
            if(rightRes.length()>resString.length()) {
                resString = rightRes;
            }
        }
        return resString;
    }

    /**
     * 回文函数
     * @param s
     * @param left
     * @param right
     * @return
     */
    public static String  plalindrome(String s, int left, int right){
        char[] arr = s.toCharArray();

        // 判断2个指针对应字符是否相等
        // 主要left、right的边界
        while(left>=0 && right<arr.length && arr[left] == arr[right]){
            left--;
            right++;
        }
        // substring(headIndex, tailIndex)，注意2个入参，第二个入参只能是尾节点的下标，不是长度，而且是下一个位置的下标。比如s="abc"，获取完整字符时s.substring(0, 3)，实际字符c的下标是2
        // 由于while先执行再判断，所以当跳出while循环时，left、right都已经越界了
        // 所以left需要加1，right不用减1，原因见上
        if(left+1 < right)
            return s.substring(left+1, right);
        return "";
    }
}
// @lc code=end
```

