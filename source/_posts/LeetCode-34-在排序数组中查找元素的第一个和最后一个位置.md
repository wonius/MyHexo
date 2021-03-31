---
title: LeetCode-34.在排序数组中查找元素的第一个和最后一个位置
copyright: true
date: 2020-12-16 16:54:11
tags: LeetCode
categories: 算法
keywords:
- 二分查找
- Binary Search
- 算法
description: 主要是左边界二分查找和右边界二分查找
---

# 题目

给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

**进阶：**

- 你可以设计并实现时间复杂度为 `O(log n)` 的算法解决此问题吗？

 

**示例 1：**

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

**示例 3：**

```
输入：nums = [], target = 0
输出：[-1,-1]
```

 

**提示：**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`
- `nums` 是一个非递减数组
- `-109 <= target <= 109`

# 题解

## Round 1

在最后把常规二分查找函数也写出来了。

便于对比左边界二分查找、右边界二分查找与常规二分查找的差异。

左边界二分查找与常规二分查找差异：

1. 当nums[mid]=target时，左边界继续循环；
2. 循环结束时，左边界判断是否越界；

右边界二分查找与常规二分查找差异：

1. mid计算时多加1，mid = left+(right-left+1)/2，这样不管nums数组是奇数还是偶数个，都会向上取整；
2. 当nums[mid]=target时，右边界继续循环；
3. 循环结束时，右边界判断是否越界；

```java
/*
 * @lc app=leetcode.cn id=34 lang=java
 *
 * [34] 在排序数组中查找元素的第一个和最后一个位置
 */

// @lc code=start
class Solution {
    public int[] searchRange(int[] nums, int target) {
        
        if(nums.length<=0) return new int[]{-1,-1};
        int leftRes = leftBoundaryBinarySearch(nums, target);
        int rightRes = rightBoundaryBinarySearch(nums, target);
        return new int[]{leftRes, rightRes};
    }

    /**
     * 左侧边界元素，即找到第一个匹配的元素
     * @param nums
     * @param target
     * @return
     */
    public int leftBoundaryBinarySearch(int[] nums, int target) {
        int mid = 0, left = 0, right = nums.length-1;

        while(left<right) {
            mid = left + (right-left)/2;
            if(target>nums[mid]) {
                left = mid+1;
            } else if(target<nums[mid]) {
                right = mid-1;
            } else if(target == nums[mid]) {
                // 左侧有可能还有相等元素，设置为右闭，继续查找
                right = mid;
            }
        }
        // 检测越界、没找到值
        if(right<0 || left>=nums.length || target!=nums[left]) return -1;
        return left;
    }

    /**
     * 右侧边界元素，即找到最后一个匹配的元素
     * @param nums
     * @param target
     * @return
     */
    public int rightBoundaryBinarySearch(int[] nums, int target) {
        int mid=0, left=0, right=nums.length-1;

        while(left<right) {
            // 加1，使mid偏右，避免陷入死循环。如下标4、5的值相同时，mid一直为4
            mid = left+(right-left+1)/2;
            if(target>nums[mid]){
                left = mid+1;
            } else if(target<nums[mid]){
                right = mid-1;
            } else if(target==nums[mid]){
                // 右侧有可能还有相等元素，设置为左闭，继续查找
                left=mid;
            }
        }
      
      	// 检测越界、没找到值
        if(right<0 || left>=nums.length || target!=nums[left]) return -1;
        return left;
    }

    /**
     * 常规二分查找，找到即返回
     * @param nums
     * @param target
     * @return
     */
    public static int binarySearch(int[] nums, int target) {
        int mid = 0, left = 0, right = nums.length-1;

        while(left<right) {
            mid = left + (right-left)/2;
            if(target < nums[mid]) {
                // 闭区间，所以right要减1
                right = mid-1;
            } else if(target > nums[mid]) {
                // 闭区间，所以left要加1
                left = mid+1;
            } else if(target == nums[mid]) {
                // 匹配到，直接return
                return mid;
            }
        }

        // 只有一个元素，没有进入循环时
        if(nums[left] != target) return -1;
        return left;
    }
}
// @lc code=end
```