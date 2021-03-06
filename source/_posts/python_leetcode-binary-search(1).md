---
title: python解LeetCode二分查找(1)
date: 2019-03-06
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   二分查找
mathjax: true
---
学习目标：

-   实现一个有序数组的二分查找算法
-   实现模糊二分查找算法
    -   大于给定值的第一个元素
-   LeetCode 69. x 的平方根
-   LeetCode 35. 搜索插入位置
-   LeetCode 34. 在排序数组中查找元素的第一个和最后一个位置

<!-- more -->

# 二分查找
查找和目标值 k 完全相等的数
```python
def BinarySearch(lst,k):
    left = 0
    right = len(lst)-1
    while left <= right:
        mid = (left+right)//2
        if lst[mid] < k:
            left = mid + 1

        elif lst[mid] > k:
            right = mid - 1

        else:
            return mid
    return -1


if __name__ == "__main__":
    lst = [1,2,3,34,56,57,78,87]
    print(BinarySearch(lst,2))
```

# 模糊二分查找
可能是跟目标值相等的数在数组中并不唯一，而是有多个，那么这种情况下nums[mid] == target这条判断语句就没有必要存在。

## 大于给定值的第一个元素
查找数组中第一个比 target 大的数的下标。
```python
def searchInsert(nums, target):
    """
    :type nums: List[int]
    :type target: int
    :rtype: int
    """
    
    left = 0
    right = len(nums)-1
    
    while left <= right:
        mid = (right+left)//2
        if nums[mid] > target:
            right = mid-1
        else:
            left = mid+1
    return right+1

if __name__ == "__main__":
    lst =[1,2,3,3,3,5,5,10,8]
    print(searchInsert(lst,5))
```
输出7

还可变形为查找最后一个不大于目标值的数。 我们已经找到了第一个大于目标值的数，那么再往前退一位，位置 - 1，就是最后一个不大于目标值的数。比如在数组[0, 1, 1, 1, 1, 5]中查找数字1，就会返回最后一个数字1的位置4。

`if nums[mid] > target:` 中的'>' 改为 '>='，就能找到**大于等于**给定值的第一个元素。往前退一位，可变形为查找最后一个小于目标值的数。

# 69. x 的平方根
实现 int sqrt(int x) 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

示例 1:
```
输入: 4
输出: 2
```
示例 2:
```
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。
```
## 方法：二分查找

```python
class Solution:
    def mySqrt(self, x: int) -> int:
        left = 0
        right = x
        
        while left <= right:
            mid = (left+right)//2
            if mid * mid < x:
                left = mid + 1
            elif mid * mid > x:
                right = mid -1
            else:
                return mid
        return left -1
```
# 35. 搜索插入位置

[Search Insert Position](https://leetcode-cn.com/problems/search-insert-position/)

## 题目描述
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

**示例 1:**

> 输入: [1,3,5,6], 5

>输出: 2

**示例 2:**

> 输入: [1,3,5,6], 2

> 输出: 1

**示例 3:**

> 输入: [1,3,5,6], 7

> 输出: 4

## 方法：二分查找
```python
class Solution(object):
    def searchInsert(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        
        left = 0
        right = len(nums)-1
        
        while left <= right:
            mid = (right-left)/2 + left
            if nums[mid] == target:
                return mid
            elif nums[mid] > target:
                right = mid-1
            else:
                left = mid+1
        return left
```

# 34. 在排序数组中查找元素的第一个和最后一个位置
给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

你的算法时间复杂度必须是 O(log n) 级别。

如果数组中不存在目标值，返回 [-1, -1]。

示例 1:
```
输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
```
示例 2:
```
输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]
```

## 方法
```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        if not nums:
            return [-1,-1]
        left = 0
        right = len(nums)-1
        
        while left <= right:
            mid = (right+left)//2
            if nums[mid] > target:
                right = mid-1
            else:
                left = mid+1
        if nums[right] != target:
            return [-1,-1]
        l = right
        while l>0 and nums[l-1] == target:
            l -= 1
        return [l,right]
```