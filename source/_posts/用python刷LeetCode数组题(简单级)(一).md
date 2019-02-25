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
# 53. 最大子序和
[Maximum Subarray](https://leetcode-cn.com/problems/maximum-subarray/)
## 题目描述
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例:**

> 输入: [-2,1,-3,4,-1,2,1,-5,4],

> 输出: 6

> 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
## 方法：Kadane's algorithm
- 用两个指针：max_so_far 指针记录此前所有碰到的最大和，max_ending_here 指针记录循环到当前元素的最大和。
- 当循环到元素i时，
    - 如果 max_ending_here < 0 的话，说明此前的和是负的，需要舍弃，所以将 max_ending_here 的值变为 i。
    - 反之，表明当前的和还是正值， 将 max_ending_here 的值变为 i + max_ending_here,可以继续向前探索。
    - max_so_far 和 max_ending_here 比较，取较大值。
 ```python
 class Solution(object):
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        max_so_far = max_ending_here = nums[0]
        
        for i in range(1,len(nums)):
            if max_ending_here < 0:
                max_ending_here = nums[i]
            else:
                max_ending_here += nums[i]
            if max_ending_here > max_so_far:
                max_so_far = max_ending_here
        return max_so_far
```
# 66. 加一
[Plus One](https://leetcode-cn.com/problems/plus-one/)
## 题目描述
给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储一个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

**示例 1:**

> 输入: [1,2,3]

> 输出: [1,2,4]

> 解释: 输入数组表示数字 123。

**示例 2:**


> 输入: [4,3,2,1]

> 输出: [4,3,2,2]

> 解释: 输入数组表示数字 4321。
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
# 88. 合并两个有序数组
[Merge Sorted Array](https://leetcode-cn.com/problems/merge-sorted-array/)
## 题目描述
给定两个有序整数数组 nums1 和 nums2，将 nums2 合并到 nums1 中，使得 num1 成为一个有序数组。

说明:
- 初始化 nums1 和 nums2 的元素数量分别为 m 和 n。
- 你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。

**示例:**

> 输入:

> nums1 = [1,2,3,0,0,0], m = 3

> nums2 = [2,5,6],       n = 3

> 输出: [1,2,2,3,5,6]

## 方法：遍历比较，大的放最后
从后往前遍历两个数组，然后把对应的元素放在数组1对应的位置，

注意：最后我们只需判断数组2有没有遍历完即可，因为数组1没有遍历完的话，它已经是按顺序放在前面的了
```python
class Solution(object):
    def merge(self, nums1, m, nums2, n):
        """
        :type nums1: List[int]
        :type m: int
        :type nums2: List[int]
        :type n: int
        :rtype: void Do not return anything, modify nums1 in-place instead.
        """
        
        while m>0 and n>0:
            if nums1[m-1] > nums2[n-1]:
                nums1[m+n-1] = nums1[m-1]
                m -= 1
            else:
                nums1[m+n-1] = nums2[n-1]
                n -= 1
        if n>0:
            nums1[:n] = nums2[:n]
```
# 118. 杨辉三角
[Pascal's Triangle](https://leetcode-cn.com/problems/pascals-triangle/)
## 题目描述
给定一个非负整数 numRows，生成杨辉三角的前 numRows 行。

在杨辉三角中，每个数是它左上方和右上方的数的和。

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
每一行前补0，后补0，相加求和即为下一行的数。
```
  1 3 3 1 0 
+ 0 1 3 3 1
= 1 4 6 4 1
```
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

# 参考
- [3个月用python刷完leetcode600题!-数组简单题（一）](https://ask.hellobi.com/blog/wenwen/8617)
- [Largest Sum Contiguous Subarray](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)