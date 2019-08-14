---
title: 《剑指offer》第2章03-15题(python)
date: 2019-08-06
categories: 
	-   Algorithm
tags:  
        -   python
        -   剑指offer
mathjax: true
---

-   第 3 题：数组中重复的数字
-   第 4 题：二维数组中的查找




<!-- more -->

# 3.数组中重复的数字
[牛客网 online judge 地址](https://www.nowcoder.com/practice/623a5ac0ea5b4e5f95552655361ae0a8?tpId=13&tqId=11203&tPage=3&rp=3&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 

例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

## 3.1 可以修改原始数组
### 方法：交换数字
交换数字到对应位置

```python
class Solution:
    # 这里要特别注意~找到任意重复的一个值并赋值到duplication[0]
    # 函数返回True/False
    def duplicate(self, numbers, duplication):
        if len(numbers)<2:
            duplication[0] = -1
            return False
        for i in range(len(numbers)):
            while i != numbers[i]:
                if numbers[i] == numbers[numbers[i]]:
                    duplication[0] = numbers[i]
                    return True
                # 注意这里不能直接multiple assignment
                temp = numbers[i]
                numbers[i] = numbers[numbers[i]]
                numbers[temp] = temp
        return False
```

## 3.2 不能修改原始数组
### 方法一：二分查找
类似于二分查找，多了一步计数

```python
class Solution:
    # 这里要特别注意~找到任意重复的一个值并赋值到duplication[0]
    # 函数返回True/False
    def duplicate(self, numbers, duplication):
        if len(set(numbers)) == len(numbers):
            duplication[0] = -1
            return False
        left = 1
        right = len(numbers)-1
        while left < right:
            mid = left + (right-left+1)//2
            count = 0
            for num in numbers:
                if num < mid:
                    count += 1
            if count < mid:# 右半边有重复
                left = mid 
            else: # 左半边有重复
                right = mid -1
        duplication[0] = left
        return True
```

### 方法二：快慢指针
使用快慢指针，判断数组中是否有逻辑上的环存在。寻找环的起点。

**注意：**题目的返回方式、快慢指针的初始化方式。

```python
class Solution:
    # 这里要特别注意~找到任意重复的一个值并赋值到duplication[0]
    # 函数返回True/False
    def duplicate(self, nums, duplication):
        if len(set(nums)) == len(nums):
            duplication[0] = -1
            return False
        fast = nums[nums[0]]
        slow = nums[0]
        while fast != slow:
            fast = nums[nums[fast]]
            slow = nums[slow]
        fast = 0
        while fast != slow:
            fast = nums[nums[fast]]
            slow = nums[slow]
        duplication[0] = nums[fast]
        return True
```