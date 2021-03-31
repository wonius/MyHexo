---
title: LeetCode-704.二分查找
copyright: true
date: 2020-12-15 15:14:29
tags: LeetCode
categories: 算法
keywords:
- 二分查找
- Binary Search
- 算法
description: Binary Search
---

# 题目

给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target` ，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。


**示例 1:**

```
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
```

**示例 2:**

```
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

# 题解

## Round 1（常规）

需要注意的几个点：

1. mid = left + (right-left)/2，等价于mid=(left+right)/2；
2. 每个if条件中对比的值，及大于、小于符号；
3. mid加1、减一；

```java
public int search(int[] nums, int target) {

        int left = 0, right = nums.length-1;
        int mid = 0;
				
        while (left < right) {
            mid = left + (right-left)/2;
          	// mid加减1，因为当前元素已经对比过了。否则while条件可能会出现死循环
            // 比如nums = [-1,0,3,5,9,12], target = 2时
            if (target > nums[mid]) {
                left = mid+1;
            } else if (target < nums[mid]) {
                right = mid-1;
            } else if (target == nums[mid]){
                return mid;
            }
        }

        if(nums[left] != target) return -1;
        return left;
    }
```

