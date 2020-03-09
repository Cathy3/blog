---
title: python解LeetCode数组(1)
date: 2019-02-22
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 数组
---

数组是一种基本的数据结构，占据一块连续的内存，并按照顺序存储数据。

创建数组时，需要首先指定数组的容量大小，然后根据大小分配内存。
-   26. 删除排序数组中的重复项
-   27. 移除元素
-   35. 搜索插入位置
-   66. 加一
-   118. 杨辉三角
-   119. 杨辉三角 II
-   167. 两数之和 II - 输入有序数组
<!-- more -->


# 26. 删除排序数组中的重复项
[Remove Duplicates from Sorted Array](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

## 题目描述
给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

**示例 1:**
```
给定数组 nums = [1,1,2], 
函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 
你不需要考虑数组中超出新长度后面的元素。
```
**示例 2:**
```
给定 nums = [0,0,1,1,1,2,2,3,3,4],
函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。
你不需要考虑数组中超出新长度后面的元素。
```
**说明:**

-   为什么返回数值是整数，但输出的答案是数组呢?
-   请注意，输入数组是以“引用”方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。
-   你可以想象内部操作如下:

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
需要一个 flag 标记：从flag=0开始，当前数与flag位置的数比较，不相同则flag+1,并替换flag+1位的数。

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

## 方法一：顺序查找
```python
class Solution(object):
    def searchInsert(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        if target > nums[-1]:
            return len(nums)
        for index, value in enumerate(nums):
            if value >= target:
                return index
```

## 方法二：二分查找
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
理论上应该比前一个方法快，实际测评结果较慢。

# 66. 加一
[Plus One](https://leetcode-cn.com/problems/plus-one/)

给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储一个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

**示例 1:**
```
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。
```
**示例 2:**
```
输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。
```

## 方法一：反过来遍历
从最后一位，找一个记录当前进位的变量，然后遍历一遍数组
```python
class Solution:
    def plusOne(self, digits):
        """
        :type digits: List[int]
        :rtype: List[int]
        """
        for i in reversed(range(len(digits))):
            if digits[i] == 9:
                digits[i] = 0
            else:
                digits[i] += 1
                return digits
        digits[0] = 1
        digits.append(0)
        return digits
```

## 方法二：转数字→字符串→int
```python
class Solution:
    def plusOne(self, digits):
        """
        :type digits: List[int]
        :rtype: List[int]
        """
        sum = 0
        for i in digits:
            sum = sum*10+i
        return [int(i) for i in str(sum+1)]
```

# 118. 杨辉三角
[Pascal's Triangle](https://leetcode-cn.com/problems/pascals-triangle/)

## 题目描述
给定一个非负整数 numRows，生成杨辉三角的前 numRows 行。

**示例:**

> 输入: 5

> 输出:

```
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```
## 方法
在杨辉三角中，每个数是它左上方和右上方的数的和。

每一行前补0，后补0，相加求和即为下一行的数。

>   1 3 3 1 0 

> \+ 0 1 3 3 1

> = 1 4 6 4 1

```python
class Solution(object):
    def generate(self, numRows):
        """
        :type numRows: int
        :rtype: List[List[int]]
        """
        if numRows == 0:
            return []
        
        res = [[1]]
        for i in range(1, numRows):
            res.append(map(lambda x,y:x+y, res[-1]+[0], [0]+res[-1]))
        return res
```
# 119. 杨辉三角 II
[Pascal's Triangle II](https://leetcode-cn.com/problems/pascals-triangle-ii/)

## 题目描述
给定一个非负索引 k，其中 k ≤ 33，返回杨辉三角的第 k 行。

**示例:**
```
输入: 3
输出: [1,3,3,1]
```
## 方法
在杨辉三角中，每个数是它左上方和右上方的数的和。

思路同上一题
```python
class Solution(object):
    def getRow(self, rowIndex):
        """
        :type rowIndex: int
        :rtype: List[int]
        """
        res = [1]
        for i in range(1, rowIndex+1):
            res = list(map(lambda x,y:x+y, res+[0], [0]+res))
        return res
```



# 167. 两数之和 II - 输入有序数组

## 题目描述
给定一个已按照升序排列 的有序数组，找到两个数使得它们相加之和等于目标数。

函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。

说明:

返回的下标值（index1 和 index2）不是从零开始的。
你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。

**示例:**

> 输入: numbers = [2, 7, 11, 15], target = 9

>输出: [1,2]

>解释: 2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

## 方法
思路同第1题
```python
class Solution(object):
    def twoSum(self, numbers, target):
        """
        :type numbers: List[int]
        :type target: int
        :rtype: List[int]
        """
        dic = dict()
        
        for index, value in enumerate(numbers):
            sub = target - value
            if sub in dic:
                return (dic[sub]+1, index+1)
            else:
                dic[value] = index
```

# 参考

-   [3个月用python刷完leetcode600题!-数组简单题（一）](https://ask.hellobi.com/blog/wenwen/8617)
-   [Largest Sum Contiguous Subarray](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)