---
title: 用python刷LeetCode数组题(简单级)(一)
date: 2019-02-20
categories: 
		- Algorithm
tags:  
		- python
        - LeetCode
		- 数组
---
# 1. 两数之和
[Two Sum](https://leetcode-cn.com/problems/two-sum/)
## 题目描述
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

**示例:**

> 给定 nums = [2, 7, 11, 15], target = 9

> 因为 nums[0] + nums[1] = 2 + 7 = 9

> 所以返回 [0, 1]
## 方法：字典
利用python中的字典记录记录下每个元素出现的位置

```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        
        dic = dict()
        
        for index,value in enumerate(nums):
            sub = target - value
            if sub in dic:
                return [dic[sub], index]
            else:
                dic[value]=index
```

# 26. 删除排序数组中的重复项
[Remove Duplicates from Sorted Array](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)
## 题目描述
给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

**示例 1:**

> 给定数组 nums = [1,1,2], 

> 函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 

> 你不需要考虑数组中超出新长度后面的元素。

**示例 2:**

> 给定 nums = [0,0,1,1,1,2,2,3,3,4],

> 函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。

> 你不需要考虑数组中超出新长度后面的元素。

**说明:**

> 为什么返回数值是整数，但输出的答案是数组呢?

> 请注意，输入数组是以“引用”方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

> 你可以想象内部操作如下:
```
// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
int len = removeDuplicates(nums);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

## 方法：遍历一次
```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        
        if not nums:
            return 0
        
        index = 0
        
        for i in range(1,len(nums)):
            if nums[i] != nums[index]:
                index += 1
                nums[index] = nums[i]
            
        return index+1
```

# 27. 移除元素
[Remove Element](https://leetcode-cn.com/problems/remove-element/)
## 题目描述
给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

**示例 1:**

> 给定 nums = [3,2,2,3], val = 3,

> 函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。

> 你不需要考虑数组中超出新长度后面的元素。

**示例 2:**

> 给定 nums = [0,1,2,2,3,0,4,2], val = 2,

> 函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

>注意这五个元素可为任意顺序。

>你不需要考虑数组中超出新长度后面的元素。
## 方法：遍历一次
```python
class Solution(object):
    def removeElement(self, nums, val):
        """
        :type nums: List[int]
        :type val: int
        :rtype: int
        """
        index = 0 
        for i in range(0,len(nums)):
            if nums[i]!=val:
                nums[index] = nums[i]
                index += 1
        return index
```
