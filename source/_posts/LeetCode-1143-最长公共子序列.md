---
title: LeetCode-1143.最长公共子序列
copyright: true
date: 2020-12-09 17:48:41
tags: LeetCode
categories: 算法
keywords:
- 最长公共子序列
- 动态规划
- 递归
- 算法
- LCS
description: 最长公共子序列 - Longest Common Subsequence，简称LCS
---

​	做这道题花了点时间，记录下。

注：子序列可以不连续。子串一定是连续的。

# 题目

给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长公共子序列的长度。

一个字符串的 *子序列* 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。

若这两个字符串没有公共子序列，则返回 0。

**示例 1:**

```
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace"，它的长度为 3。
```

**示例 2:**

```
输入：text1 = "abc", text2 = "abc"
输出：3
解释：最长公共子序列是 "abc"，它的长度为 3。
```

**示例 3:**

```
输入：text1 = "abc", text2 = "def"
输出：0
解释：两个字符串没有公共子序列，返回 0。
```

# 题解

## Round 1（失败）

思路：

既然获取公共子序列，那以短的句子为基础，遍历每个字符，判断在长句子中是否存在，如果存在则保存当前字符。最终得到公共子序列中。任意一个字符串结束都算结束。

乍一看这个思路没问题，实际不然。缺陷在下方也有说明。

```java
/*
 * @lc app=leetcode.cn id=1143 lang=java
 *
 * [1143] 最长公共子序列
 */

// @lc code=start
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        
        if(text1 == null || text2 == null
            || text1.isEmpty() || text2.isEmpty()) return 0;

        /**
         * 判断2个句子长短，以短句为基准，挨个判断每个字符是否存在长句中，如果存在，则为相同字符，保存。最后得出最长的字符组合；
         * 这个算法有个致命缺陷，如果短句中间某个字符恰好等于长句的最后1个字符，那对比就会提前结束，得到错误结果
         * 比如有2个字符串：
         * mhunuzqrkzsnidwbun
         * szulspmhwpazoxijwbq
         * 按照该算法，得到的结果是mhzq，但是最长的是mhziwb
         */
         String longString = null;
         char[] array = null;
         int res = 0;    // 保存最终结果
         int tempResInt =0;  //中间过程长度

         // 将短句拆分为数组，长句保存到longString，用于下面比对
         if(text1.length()<text2.length()) {
             array = text1.toCharArray();
             longString = text2;
         }else {
             array = text2.toCharArray();
             longString = text1;
         }

         // 挨个遍历短句数组中的元素
         for(int i = 0; i<array.length; i++){
             tempResInt = 0;
             StringBuilder tempResString = new StringBuilder() ;
             String tempString = longString;
             // 从当前字符开始
             for(int j = i; j<array.length; j++ ) {
                 char cur = array[j];
                 // 判断长句剩下的子串中是否存在当前字符，有的话计数
                 if (tempString.indexOf(cur) != -1) {
                     tempResInt++;
                     tempResString.append(cur);
                     tempString = tempString.substring(tempString.indexOf(cur)+1);
                 }
             }
             System.out.println("tempResInt: "+ tempResInt + ", tempResString: "+tempResString.toString());
             // 保存最长的结果
             res = Math.max(tempResInt, res);
         }
         System.out.println("length: "+ res + ", tempResInt: ");
         return res;
    }

}
```

## Round 2（递归）

思路：

动态规划的思路解题。推荐看下labuladong的算法小抄。

```java
/*
 * @lc app=leetcode.cn id=1143 lang=java
 *
 * [1143] 最长公共子序列
 */

// @lc code=start
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        
        if(text1 == null || text2 == null
            || text1.isEmpty() || text2.isEmpty()) return 0;

        /**
         * 2. 递归法
         * 关键是梳理出来递归方法，哪些状态要在递归里处理
         * 递归法耗时太长
         */
         char[] arr1 = text1.toCharArray();
         char[] arr2 = text2.toCharArray();

         // 从尾部往前逐个字符对比。数组从0开始，所以长度减1
         return recursion(arr1, arr2, arr1.length-1, arr2.length-1);

    }

    /**
     * 递归数组，状态变化体现在参数中
     */
     public int recursion(char[] text1, char[] text2, int i, int j){
         // 任意字符数组遍历完，结束
         if (i < 0 || j < 0) {
             return 0;
         }

         // 如果字符相等，同时进1。结果加1
         if (text1[i] == text2[j]) {
             return recursion(text1, text2, i-1, j-1)+1;
         } else {
             // 不相等，一个句子往前进1，另外一个不变。2种情况取最长
             return Math.max(recursion(text1, text2, i-1, j), recursion(text1, text2, i, j-1));
         }
     }
}
```

## Round 3（数组存储结果）

思路：

与递归法类似，区别在于，将每种情况的结果存放到数组中。

```java
/*
 * @lc app=leetcode.cn id=1143 lang=java
 *
 * [1143] 最长公共子序列
 */

// @lc code=start
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        
        if(text1 == null || text2 == null
            || text1.isEmpty() || text2.isEmpty()) return 0;

        /**
         * 3. dp数组法
         * 参照递归法，将每个步骤的使用数组存储起来
         * 算法过程与递归相同
         */

        char[] arr1 = text1.toCharArray();
        char[] arr2 = text2.toCharArray();
        int length1 = text1.length();
        int length2 = text2.length();
        
        // 创建dp数组，保存结果。长度多加1位，因为存在dp[0][j]、dp[i][0]的情况，这种情况值都为0
        int[][] dp = new int[length1+1][length2+1];

        // 从1开始，是因为当i、j从0开始时，i-1会小于0
        for (int i = 1; i < length1+1; i++) {
            for (int j = 1; j < length2+1; j++) {
                if (arr1[i-1] == arr2[j-1]) {
                    dp[i][j] = dp[i-1][j-1]+1;
                } else {
                    dp[i][j] = Math.max(dp[i][j-1], dp[i-1][j]);
                }
            }
        }
        // 返回2个最长字符串的结果
        return dp[length1][length2];

    }

}
```

