---
title: python实现数据结构中的数组
date: 2019-02-28
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 数组
---
在数组中，访问数据十分简单，而添加和删除数据比较耗工夫。

由于数组中的数据是存储在连续空间内的，所以每个数据的内存地址（在内存上的位置）都可以通过数组下标算出，我们也就可以借此直接访问目标数据（这叫作“随机访问”），需要的运行时间仅为恒定的 `O(1)`。 

向数组中添加新数据时，必须把目标位置后面的数据一个个移开。所以，如果在数组头部添加数据，就需要`O(n)`的时间。删除操作同理。


学习目标：

-   实现一个支持动态扩容的数组
-   实现一个大小固定的有序数组，支持动态增删改操作
-   Leetcode 88. [合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)
-   Leetcode 1. [两数之和](https://leetcode-cn.com/problems/two-sum/)
-   15. 三数之和
-   16. 最接近的三数之和

<!-- more -->


# 支持动态扩容的数组
类似于Python的简化版list
```python
import ctypes
 
class DynamicArray:   
    def __init__(self):
        """ Create an empty array."""
        self._n = 0 #count actual elements
        self._capacity = 1 #default array capacity
        self._A = self._make_array(self._capacity)  #low-level array
        
    def __len__(self):
        """Return number of elements stored in the array."""
        return self._n
    
    def __getitem__(self,k):
        """Return element at index k."""
        if not 0 <= k < self._n:
            raise IndexError('invalid index')
        return self._A[k]  #retrieve from array
    
    def append(self, obj):
        """Add object to end of the array."""
        if self._n == self._capacity:  # not enough room
            self._resize(2*self._capacity) #so double capacity
        self._A[self._n] = obj
        self._n += 1
        
    def _resize(self, c):  # nonpublic utitity
        """Resize internal array to capacity c."""
        B = self._make_array(c)   # new (bigger) array
        for k in range(self._n):  # for each existing value
            B[k] = self._A[k]
        self._A = B
        self._capacity = c
    
    def _make_array(self, c):  # nonpublic utitity   
        """Return new array with capacity c."""
        return (c * ctypes.py_object)() # see ctypes documentation
 
if __name__ == '__main__':
    
    da = DynamicArray()
    da.append(6)
    da.append(2)
    
    for o in da:
        print(o)
```
输出：

```
6
2
```

# 大小固定的有序数组，支持动态增删改操作
```python
class NewArray:
    """ A dynamic array class akin to a simplified Python list."""
    
    def __init__(self,capacity=10):
        """ Create an empty array."""
        self._n = 0  #数组大小
        self._A = [None] * capacity
    
    def __getitem__(self,k):
        """Return element at index k."""
        if not 0 <= k < self._n:
            raise IndexError('invalid index')
        return self._A[k] #retrieve from array
    
    def insert(self,k,v):
        if k >= len(self._A):
            raise IndexError('Index out of range!')
        for i in range(self._n, k-1, -1):
            self._A[i+1] = self._A[i]
        self._A[k] = v
        self._n += 1
        
    def delete(self,k):
        v = self._A[k]
        for i in range(k+1,self._n):
            self._A[i-1] = self._A[i]
        self._n -= 1
        return v
    
    def __setitem__(self,k,v):
        self._A[k] = v
 
if __name__ == '__main__':
    
    A = NewArray()
    for i in range(5):
        A.insert(i, i) # 增
    print(A[2]) 
    A.delete(0) # 删
    print(A[2])
    A[0] = 10 # 改
    print(A[0]) # 查
```
输出：
```
2
3
10
```

# 88. 合并两个有序数组

给定两个有序整数数组 nums1 和 nums2，将 nums2 合并到 nums1 中，使得 num1 成为一个有序数组。

说明:

-   初始化 nums1 和 nums2 的元素数量分别为 m 和 n。
-   你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。

**示例:**
```
输入:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3
输出: [1,2,2,3,5,6]
```

## 方法：遍历比较，大的放最后
从后往前遍历两个数组，然后把对应的元素放在数组1对应的位置，

注意：最后我们只需判断数组2有没有遍历完即可，因为数组1没有遍历完的话，它已经是按顺序放在前面的了。

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

# 1. 两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那**两个**整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

**示例:**

```
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

## 方法：哈希表
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
# 15. 三数之和
给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。
```
例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
[-1, 0, 1],
[-1, -1, 2]
]
```

## 方法：排序，循环Two_Sum
```python
class Solution:
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums= sorted(nums)  #排序 O(nlogn)
        output = set()
        for k in range(len(nums)):
            target = -nums[k]
            i, j = k + 1, len(nums)-1
            while  i < j:
                sum_two = nums[i] + nums[j]
                if sum_two < target:
                    i += 1
                elif sum_two >target:
                    j -= 1
                else:
                    output.add((nums[k], nums[i], nums[j]))
                    i += 1
                    j -= 1
        return list(set(output))
```

Time Complexity: O(n2) 

# 16.最接近的三数之和
给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

例如，给定数组 nums = [-1，2，1，-4], 和 target = 1.

与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).

## 方法：排序，循环比较三种情况
-   等于，则返回
-   大于，则最后的指针-1
-   小于，则首位的指针+1

```python
class Solution:
    def threeSumClosest(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        nums.sort() # 排序
        
        n = len(nums)
        d = 2**32
        ans = 0
        
        for i in range(n-2):
            s, e = i+1, n-1
            while s < e:
                sum3 = nums[i]+nums[s]+nums[e]
                if sum3 == target: return target
                diff = abs(sum3 - target)
                if diff < d: 
                    ans, d = sum3, diff
                if sum3 > target:
                    e -= 1
                else:
                    s += 1
        return ans
```

# 参考
- [mplementation of Dynamic Array in Python](https://www.geeksforgeeks.org/implementation-of-dynamic-array-in-python/)
- [任务1-数组与链表](https://note.youdao.com/ynoteshare1/index.html?id=6b47b08c91b05db6c33fe92b88b53192&type=note#/)