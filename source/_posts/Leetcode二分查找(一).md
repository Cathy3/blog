---
title: python刷LeetCode二分查找题(简单级)
date: 2019-06-10
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 二分查找
---
   
-   278. 第一个错误的版本
-   33. 搜索旋转排序数组
-   81. 搜索旋转排序数组 II
-   153. 寻找旋转排序数组中的最小值    

<!-- more -->

# 278. 第一个错误的版本
你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。

你可以通过调用 bool isBadVersion(version) 接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

示例:
```
给定 n = 5，并且 version = 4 是第一个错误的版本。

调用 isBadVersion(3) -> false
调用 isBadVersion(5) -> true
调用 isBadVersion(4) -> true

所以，4 是第一个错误的版本。 
```

## 方法

```python
class Solution:
    def firstBadVersion(self, n):
        """
        :type n: int
        :rtype: int
        """
        left = 0
        right = n-1
        while left <= right:
            mid = left + (right-left)//2
            if isBadVersion(mid):
                right = mid - 1
            else:
                left = mid + 1
        return left
```

## 33. 搜索旋转排序数组 
假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 O(log n) 级别。

示例 1:
```
输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
```
示例 2:
```
输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1
```

## 方法
我们的二分查找的思想就是找出某个条件，这个条件给了我们移动左右指针的参考，即要判断查找的target在mid的左边还是右边。

具体到这个题目，因为给出的数组是旋转有序的，如果mid指向的位置在于pivot之后，那么mid向后部分是有序的，这个时候需要判断target在mid左边还是右边，最简单的方法就是判断target是不是在[pivot,r]区间内，如果的话就向mid后半部分搜索，否则就向mid左半部分搜索；同理，当mid在pivot之前，那么mid前面部分是有序的，根据target判断下面要向mid的左边还是右边搜索。

具体来说，假设数组是A，每次左边缘为l，右边缘为r，还有中间位置是m。在每次迭代中，分三种情况：

1.   如果target==A[m]，那么m就是我们要的结果，直接返回；
2.   如果A[m]<A[r]，那么说明从m到r一定是有序的（没有受到rotate的影响），那么我们只需要判断target是不是在m到r之间，如果是则把左边缘移到m+1，否则就target在另一半，即把右边缘移到m-1。
3.   如果A[m]>=A[r]，那么说明从l到m一定是有序的，同样只需要判断target是否在这个范围内，相应的移动边缘即可。

注意，由于这个题目要进行和边缘元素的判断，所以没有采取[l,r)的左闭右开区间，而是使用了[l, r]双闭区间。

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        if not nums: return -1
        left = 0
        right = len(nums)-1
        while left <= right:
            mid = (left + right)//2
            if nums[mid] == target:
                return mid
            if nums[mid] < nums[right]: # 确定右边有序
                if nums[mid] < target and target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1
            else: # 确定左边有序
                if nums[mid] > target and target >= nums[left]:
                    right = mid - 1
                else:
                    left = mid + 1
        return -1
```

# 81. 搜索旋转排序数组 II
假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。

编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。

示例 1:
```
输入: nums = [2,5,6,0,0,1,2], target = 0
输出: true
```
示例 2:
```
输入: nums = [2,5,6,0,0,1,2], target = 3
输出: false
```

## 方法
是第33题的拓展题目，变的是加了一个可能含有重复数字。

这样的话，如果直接进行左右指针的比较就不知道向哪个方向搜索了，

所以，需要在正式比较之前，先移动左指针，是他指向一个和右指针不同的数字上。然后再做33题的查找。

```python
class Solution:
    def search(self, nums: List[int], target: int) -> bool:
        left = 0
        right = len(nums)-1
        while left <= right:
            while left < right and nums[left] == nums[right]:
                left += 1
            mid = (left + right)//2
            if nums[mid] == target:
                return True
            if nums[left] <= nums[mid]: # 前半部有序
                if nums[left] <= target and target < nums[mid]: # 有一部分要写‘=’号
                    right = mid - 1
                else:
                    left = mid + 1
            else: # 后半部分有序
                if nums[mid] < target and target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1
        return False
```

# 153. 寻找旋转排序数组中的最小值
假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

请找出其中最小的元素。

你可以假设数组中不存在重复元素。

示例 1:
```
输入: [3,4,5,1,2]
输出: 1
```
示例 2:
```
输入: [4,5,6,7,0,1,2]
输出: 0
```

## 方法
这个题是剑指offer上的原题。看到有序的数组就想到二分查找。这个是变种而已。

注意边界和循环条件。

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if len(nums) == 1:return nums[0]
        left, right = 0, len(nums)-1
        
        mid = left
        while nums[left] >= nums[right]: # 左半边 大于 右半边
            if left+1  == right: # 左右边相邻
                mid = right
                break
            mid = (left + right)//2
            if nums[left] <= nums[mid]:
                left = mid
            elif nums[mid] <= nums[right]:
                right = mid
                
        return nums[mid]
```

# 参考
-    [【LeetCode】153. Find Minimum in Rotated Sorted Array 解题报告（Python）](https://blog.csdn.net/fuxuemingzhu/article/details/79533470)