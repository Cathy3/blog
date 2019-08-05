---
title: python解LeetCode数组题(简单)(2)
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

```
输入: [3,2,3]
输出: 3
```

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


**示例 1:**

```
输入: [1,2,3,4,5,6,7] 和 k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右旋转 1 步: [7,1,2,3,4,5,6]
向右旋转 2 步: [6,7,1,2,3,4,5]
向右旋转 3 步: [5,6,7,1,2,3,4]
```

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

283. 移动零
[Move Zeroes](https://leetcode-cn.com/problems/move-zeroes/)

## 题目描述
给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

示例:
```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```
说明:

1. 必须在原数组上操作，不能拷贝额外的数组。
2. 尽量减少操作次数。

## 方法
遍历数组，用一个变量记录当前不为0的个数，同时也是新不为零元素插入的位置：
```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        index = 0
        for i in nums:
            if i != 0:
                nums[index]=i
                index += 1
        for i in range(index,len(nums)):
            nums[i] = 0
```

# 414. 第三大的数

给定一个非空数组，返回此数组中第三大的数。如果不存在，则返回数组中最大的数。要求算法时间复杂度必须是O(n)。

示例 1:
```
输入: [3, 2, 1]

输出: 1

解释: 第三大的数是 1.
```
示例 2:
```
输入: [1, 2]

输出: 2

解释: 第三大的数不存在, 所以返回最大的数 2 .
```
示例 3:
```
输入: [2, 2, 3, 1]

输出: 1

解释: 注意，要求返回第三大的数，是指第三大且唯一出现的数。
存在两个值为2的数，它们都排第二。
```
## 方法一
```python
class Solution:
    def thirdMax(self, nums: List[int]) -> int:
        max1 = max2 = max3 = float('-inf')
        for num in nums:
            if num>max1:
                max2,max3 = max1,max2
                max1 = num
            elif num>max2 and num < max1:
                max2, max3 = num,max2
            elif num>max3 and num < max2:
                max3 = num
        return max1 if max3==float('-inf') else max3
```

## 方法二
```python
class Solution:
    def thirdMax(self, nums: List[int]) -> int:
        a=set(nums)
        if len(a)<3: return max(a)
        else: 
            a.remove(max(a))
            a.remove(max(a))
            return max(a)
```

# 448. 找到所有数组中消失的数字
给定一个范围在  1 ≤ a[i] ≤ n ( n = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。

找到所有在 [1, n] 范围之间没有出现在数组中的数字。

您能在不使用额外空间且时间复杂度为O(n)的情况下完成这个任务吗? 你可以假定返回的数组不算在额外空间内。

示例:
```
输入:
[4,3,2,7,8,2,3,1]

输出:
[5,6]
```

## 方法
注意到数组元素个数跟元素范围是一样的，所以我们可以把出现过的元素对应下标位置的数字变成负数，最后把所有正数对应的下标拿出来，就是缺失的数字。
```python
class Solution:
    def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
        for i in range(len(nums)):
            index = abs(nums[i])-1
            nums[index] = -abs(nums[index])
        return [i+1 for i in range(len(nums)) if nums[i]>0]
```

# 485. 最大连续1的个数
给定一个二进制数组， 计算其中最大连续1的个数。

示例 1:
```
输入: [1,1,0,1,1,1]
输出: 3
解释: 开头的两位和最后的三位都是连续1，所以最大连续1的个数是 3.
```
注意：

-   输入的数组只包含 0 和1。
-   输入数组的长度是正整数，且不超过 10,000。

## 方法：遍历数组
```python
class Solution:
    def findMaxConsecutiveOnes(self, nums: List[int]) -> int:
        cnt = 0
        ans = 0
        for num in nums:
            if num==1:
                cnt += 1
                ans = max(ans,cnt)
            else:
                cnt = 0
        return ans
```

# 532. 数组中的K-diff数对
给定一个整数数组和一个整数 k, 你需要在数组里找到不同的 k-diff 数对。这里将 k-diff 数对定义为一个整数对 (i, j), 其中 i 和 j 都是数组中的数字，且两数之差的绝对值是 k.

示例 1:
```
输入: [3, 1, 4, 1, 5], k = 2
输出: 2
解释: 数组中有两个 2-diff 数对, (1, 3) 和 (3, 5)。
尽管数组中有两个1，但我们只应返回不同的数对的数量。
```
示例 2:
```
输入:[1, 2, 3, 4, 5], k = 1
输出: 4
解释: 数组中有四个 1-diff 数对, (1, 2), (2, 3), (3, 4) 和 (4, 5)。
```
示例 3:
```
输入: [1, 3, 1, 5, 4], k = 0
输出: 1
解释: 数组中只有一个 0-diff 数对，(1, 1)。
```
注意:
```
数对 (i, j) 和数对 (j, i) 被算作同一数对。
数组的长度不超过10,000。
所有输入的整数的范围在 [-1e7, 1e7]。
```

## 方法
如果k大于0，则返回两个set的交集
如果k=0，则计数，找出出现1次以上的元素的个数
如果k小于0，返回0
```python
class Solution:
    def findPairs(self, nums: List[int], k: int) -> int:
        if k>0:
            return len(set(nums) & set(num+k for num in nums))
        elif k==0:
            return sum(v>1 for v in collections.Counter(nums).values())
        else:
            return 0
```

# 561. 数组拆分 I
给定长度为 2n 的数组, 你的任务是将这些数分成 n 对, 例如 (a1, b1), (a2, b2), ..., (an, bn) ，使得从1 到 n 的 min(ai, bi) 总和最大。

示例 1:
```
输入: [1,4,3,2]

输出: 4
解释: n 等于 2, 最大总和为 4 = min(1, 2) + min(3, 4).
```
提示:

1.  n 是正整数,范围在 [1, 10000].
2.  数组中的元素范围在 [-10000, 10000].


## 方法
返回排序后第1，3，5，。。2n-1小的数
```python
class Solution:
    def arrayPairSum(self, nums: List[int]) -> int:
        return sum(sorted(nums)[::2])
```

# 566. 重塑矩阵
在MATLAB中，有一个非常有用的函数 reshape，它可以将一个矩阵重塑为另一个大小不同的新矩阵，但保留其原始数据。

给出一个由二维数组表示的矩阵，以及两个正整数r和c，分别表示想要的重构的矩阵的行数和列数。

重构后的矩阵需要将原始矩阵的所有元素以相同的行遍历顺序填充。

如果具有给定参数的reshape操作是可行且合理的，则输出新的重塑矩阵；否则，输出原始矩阵。

示例 1:
```
输入: 
nums = 
[[1,2],
 [3,4]]
r = 1, c = 4
输出: 
[[1,2,3,4]]
解释:
行遍历nums的结果是 [1,2,3,4]。新的矩阵是 1 * 4 矩阵, 用之前的元素值一行一行填充新矩阵。
```
示例 2:
```
输入: 
nums = 
[[1,2],
 [3,4]]
r = 2, c = 4
输出: 
[[1,2],
 [3,4]]
解释:
没有办法将 2 * 2 矩阵转化为 2 * 4 矩阵。 所以输出原矩阵。
```
注意：

1.  给定矩阵的宽和高范围在 [1, 100]。
2.  给定的 r 和 c 都是正数。

## 方法
将数组转换为1行，然后根据要转换的行列数进行转换
```python
class Solution:
    def matrixReshape(self, nums: List[List[int]], r: int, c: int) -> List[List[int]]:
        if len(nums)*len(nums[0]) != r*c:
            return nums
        else:
            onerow = [nums[i][j] for i in range(len(nums)) for j in range(len(nums[0]))]
            return [onerow[t*c:(t+1)*c] for t in range(r)]
```

# 581. 最短无序连续子数组
给定一个整数数组，你需要寻找一个连续的子数组，如果对这个子数组进行升序排序，那么整个数组都会变为升序排序。

你找到的子数组应是最短的，请输出它的长度。

示例 1:
```
输入: [2, 6, 4, 8, 10, 9, 15]
输出: 5
解释: 你只需要对 [6, 4, 8, 10, 9] 进行升序排序，那么整个表都会变为升序排序。
```
说明 :

1.  输入的数组长度范围在 [1, 10,000]。
2.  输入的数组可能包含重复元素 ，所以升序的意思是<=。

## 方法
`all_same.index(False)`是元素从前往后看第一个为False的元素的序数。`all_same[::-1]`是从后往前看。
```python
class Solution:
    def findUnsortedSubarray(self, nums: List[int]) -> int:
        all_same = [a==b for (a,b) in zip(nums, sorted(nums))]
        return 0 if all(all_same) else len(nums)-all_same.index(False)-all_same[::-1].index(False)
```


# 参考
[3个月用python刷完leetcode600题!-数组简单题（二）](https://ask.hellobi.com/blog/wenwen/8618)