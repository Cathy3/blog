---
title: 用python刷LeetCode数组题(简单级)(一)
date: 2019-02-22
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 数组
---

<!-- more -->

# 1. 两数之和
[Two Sum](https://leetcode-cn.com/problems/two-sum/)

## 题目描述
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那**两个**整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。



**示例:**
```
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```
## 方法：字典
利用python中的字典记录记录下每个元素出现的位置。key记差值，value记已有数值的位置。

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

## 方法： 动态规划

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        maxsum = res = nums[0]
        for i in range(1, len(nums)):
            maxsum = max(maxsum + nums[i], nums[i])
            res = max(maxsum, res)
        return res
```
结果不如前一种方法快

# 66. 加一
[Plus One](https://leetcode-cn.com/problems/plus-one/)

## 题目描述
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

在杨辉三角中，每个数是它左上方和右上方的数的和。

**示例:**
```
输入: 3
输出: [1,3,3,1]
```
## 方法
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

# 121. 买卖股票的最佳时机
[Best Time to Buy and Sell Stock](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

## 题目描述
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

**示例 1:**
```
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
```
**示例 2:**
```
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

## 方法一：遍历
根据题意，我们只需要找出数组中最大的差值即可，即 `max(prices[j] – prices[i]) ，i < j` 。
如何得到最大的差值，只需要一次遍历即可，在遍历的用一个变量记录遍历到当前时的最小值即可。时间复杂度为 `O(n)`.
```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if prices is None or len(prices)<2:
            return 0
        
        buy = prices[0] # 买入
        profit = 0 # 利润
        
        for i in range(1,len(prices)):
            if prices[i] < buy:
                buy = prices[i]
            else:
                if prices[i] - buy > profit:
                    profit = prices[i] - buy
        return profit
```

## 方法二：Kadane's algorithm
- 方法同上面的53题-求数组中和最大的连续子数组序列。
- 如何转化为求数组中的和最大的连续子序列？相邻两个数作差即可，这样的话子序列的和就是我们在子序列开始卖出股票，在子序列最后买回股票所能得到的收益。
```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        
        max_so_far = max_ending_here = 0
        
        for i in range(1, len(prices)):
            max_ending_here = max(0, max_ending_here + prices[i] - prices[i-1])
            max_so_far = max(max_so_far, max_ending_here)
        return max_so_far
```
测试结果比前一个方法慢

# 122. 买卖股票的最佳时机 II
[Best Time to Buy and Sell Stock II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

## 题目描述
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例 1:**

输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。

**示例 2:**

输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。

**示例 3:**

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

## 方法：遍历
如果后面的股价比前面的大，我们就买卖
```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        return sum(max(prices[i+1]-prices[i], 0) for i in range(len(prices)-1))
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