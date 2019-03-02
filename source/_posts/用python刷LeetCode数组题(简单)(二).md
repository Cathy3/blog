---
title: 用python刷LeetCode数组题(简单级)(二)
date: 2019-02-26
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 数组
---
# 169. 求众数
Majority Element
## 题目描述
给定一个大小为 n 的数组，找到其中的众数。众数是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在众数。

**示例 1:**

> 输入: [3,2,3]

>输出: 3

<!-- more -->

## 方法： 遍历
最多的元素出现的次数大于数组长度的一半，在最极端的情况下，最后的count都会大于0
```python
class Solution(object):
    def majorityElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        count = 1
        res = nums[0]
        
        for i in range(1, len(nums)):
            if nums[i] == res:
                count += 1
            else:
                count -= 1
            
            if count == 0:
                res = nums[i]
                count = 1
        return res
```

# 189. 旋转数组
Rotate Array
## 题目描述
给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

示例 1:

输入: [1,2,3,4,5,6,7] 和 k = 3

输出: [5,6,7,1,2,3,4]

解释:

向右旋转 1 步: [7,1,2,3,4,5,6]

向右旋转 2 步: [6,7,1,2,3,4,5]

向右旋转 3 步: [5,6,7,1,2,3,4]

## 方法：直接拼接数组

```python
class Solution(object):
    def rotate(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: None Do not return anything, modify nums in-place instead.
        """
        n = len(nums)
        nums[:] = nums[n-k:] + nums[:n-k]
```

# 217. 存在重复元素
Contains Duplicate
## 题目描述
给定一个整数数组，判断是否存在重复元素。

如果任何值在数组中出现至少两次，函数返回 true。如果数组中每个元素都不相同，则返回 false。

示例 1:

输入: [1,2,3,1]
输出: true

示例 2:

输入: [1,2,3,4]
输出: false


## 方法：set()
```python
class Solution(object):
    def containsDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        return len(nums) != len(set(nums))
```

# 219. 存在重复元素 II
Contains Duplicate II
## 题目描述
给定一个整数数组和一个整数 k，判断数组中是否存在两个不同的索引 i 和 j，使得 nums [i] = nums [j]，并且 i 和 j 的差的绝对值最大为 k。

**示例 1:**

> 输入: nums = [1,0,1,1], k = 1

> 输出: true

**示例 2:**

> 输入: nums = [1,2,3,1,2,3], k = 2

> 输出: false

## 方法
用dict保存数组元素出现的位置，两种情况下更新
```python
class Solution(object):
    def containsNearbyDuplicate(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: bool
        """
        dic = dict()
        
        for index,value in enumerate(nums):
            if value in dic and index - dic[value] <= k:
                return True
            dic[value] = index
        return False
```
# 268. 缺失数字
Missing Number
## 题目描述
给定一个包含 0, 1, 2, ..., n 中 n 个数的序列，找出 0 .. n 中没有出现在序列中的那个数。

**示例 1:**

> 输入: [3,0,1]
> 输出: 2

**示例 2:**

> 输入: [9,6,4,2,3,5,7,0,1]

> 输出: 8

## 方法：数学公式
```python
class Solution(object):
    def missingNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        n = len(nums)
        
        return n* (n+1)/2 - sum(nums)
```

