---
title: LeetCode-300.最长上升子序列
copyright: true
date: 2020-12-14 15:51:39
tags: LeetCode
categories: 算法
keywords:
- 最长上升子序列
- 动态规划
- 二叉树
- 算法
- LIS
description: 最长上升子序列 - Longesg Increasing Subsequence，简称LIS
---

# 题目

给定一个无序的整数数组，找到其中最长上升子序列的长度。

**示例:**

```
输入: [10,9,2,5,3,7,101,18]
输出: 4 
解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
```

**说明:**

- 可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。
- 你算法的时间复杂度应该为 O(*n2*) 。

**进阶:** 你能将算法的时间复杂度降低到 O(*n* log *n*) 吗?

# 题解

## Round 1（失败）

思路：

解子序列问题要用动态规划，解动态规划先写出来递归解法，然后再转换成dp数组存储中间变量。

递归函数如下，可惜不对。dp(nums, i)的值，并不等于dp(nums, i-1)+1

```java
    public int dp(int[] nums, int i) {
        if(i < 1) return 1;
        if(i == 1 && nums[i]>nums[i-1]) return 1;
        if(nums[i]>nums[i-1]) {
            return dp(nums, i-1)+1;
        } else{
            return dp(nums, i-1);
        }
    }
```

发现不对后，然后换了种思路，尝试将数组转换成矩阵的方式，即横轴、纵轴皆为这个数组，然后对比大小。但是这种也不对，比出来的大小值彼此间没有任何关联。

归根结底，是没有找到不同元素对应值之间的关系，即假设：y(x)=f(x)，y(x+1)=f(x+1)时，有y(x+1)=F[y(x)]，要找到F()这个函数做的事情。（f()、F()都是函数，初中数学没忘吧）

## Round 2

解：当前元素对应的值，等于前面比当前元素小对应的值中最大的值，加1

即，F[y(x)]=Max{y(0),y(1)...y(x-1)}+1，所以 F[y(x)]=Max{f(0),f(1)...f(x-1)}+1

也就是， F[x]=Max{f(0),f(1)...f(x-1)}+1

x是任意值，所以f()和F()等价，得：f(x)=Max{f(0),f(1)...f(x-1)}+1

```java
/*
 * @lc app=leetcode.cn id=300 lang=java
 *
 * [300] 最长上升子序列
 */

// @lc code=start
class Solution {
    public int lengthOfLIS(int[] nums) {


        /**
         * 用dp数组存储每个元素的值
         * 最终取最大的值
         * 
         * 其中关键是找到当前元素对应的值，与前一个元素对应的值之间的关系
         */
        int res = 0;
        int length = nums.length;
        int[] dp = new int[length+1];
        Arrays.fill(dp, 1);

        // 计算每个元素对应的值
        for (int i = 0; i < length; i++) {
            for (int j = 0; j < i; j++) {
                // 当前元素的值，是在前面比他小的最大值+1
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j]+1);
                }
            }
        }

        // 取最大值
        for (int i = 0; i < dp.length; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }

}
// @lc code=end
```

