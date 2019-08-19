---
title: python解LeetCode数组(3)
date: 2019-08-11
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 数组
---

-   4. 寻找两个有序数组的中位数
-   11. 盛最多水的容器
-   54. 螺旋矩阵
-   59. 螺旋矩阵 II
-   136. 只出现一次的数字
-   977. 有序数组的平方
-   240. 搜索二维矩阵 II
-   74. 搜索二维矩阵
-   347. 前 K 个高频元素
-   560. 和为K的子数组

<!-- more -->

# 4. 寻找两个有序数组的中位数
[寻找两个有序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m+n))。

你可以假设 nums1 和 nums2 不会同时为空。

示例 1:
```
nums1 = [1, 3]
nums2 = [2]
则中位数是 2.0
```
示例 2:
```
nums1 = [1, 2]
nums2 = [3, 4]
则中位数是 (2 + 3)/2 = 2.5
```

## 方法一：合并，排序，除以2
对于一个有序数组，如果数组长度是奇数，那么中位数就是中间那个值，如果长度是偶数，就是中间两个数的平均数。
```python
class Solution:
    def findMedianSortedArrays(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: float
        """
        tmp = nums1 + nums2
        tmp.sort()
        if len(tmp)%2==1:
            return tmp[int(len(tmp)/2)]
        else:
            return (tmp[int(len(tmp)/2)-1] + tmp[int(len(tmp)/2)])/2.0
```

Time Complexity: O(n) 

大于题目要求的时间复杂度

## 方法二：寻找划分位置
中位数的作用：将一个集合划分为两个长度相等的子集，其中一个子集中的元素总是大于另一个子集中的元素。

思路：nums1和nums2分别以i,j为界划分成左右两部分，不断调整i,j,以达成以下条件：

1.  len(nums1_左) + len(nums2_左) = len(nums1_右) + len(nums2_右) 就是说，划分后的左右两边的长度相等。
2.  Max(nums1左、nums2左) ≤ Min(nums1右、nums2右) 就是说，左边的元素总是小于右边。

```python
class Solution:
    def findMedianSortedArrays(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: float
        """
        m, n = len(nums1), len(nums2)
        if m > n:
            nums1, nums2, m, n = nums2, nums1, n, m
        if n == 0:
            raise ValueError
            
        imin, imax, half_len = 0, m, (m+n+1)//2
        while imin <= imax:
            i = (imin + imax)//2
            j = half_len - i
            if i<m and nums2[j-1] > nums1[i]: #i太小
                imin = i+1
            elif i>0 and nums1[i-1]>nums2[j]: #i太大
                imax = i-1
            else:#找到i的正确位置
                if i==0: max_of_left = nums2[j-1]#临界问题，其中一个列表全在右边，左边为空
                elif j==0: max_of_left = nums1[i-1]
                else: max_of_left = max(nums1[i-1], nums2[j-1])
                    
                if(m+n)%2 == 1: #若列表长度为奇数
                    return max_of_left
                
                if i==m: min_of_right = nums2[j] #临界问题，其中一个列表全在左边，右边为空
                elif j==n: min_of_right = nums1[i]
                else: min_of_right = min(nums1[i], nums2[j])
                    
                return (max_of_left + min_of_right)/2
```

Time Complexity: O(log(min(m,n))) 

首先，查找的区间是 [0,m]。 而该区间的长度在每次循环之后都会减少为原来的一半。 所以，我们只需要执行 \log(m) 次循环。由于我们在每次循环中进行常量次数的操作，所以时间复杂度为 O(log(m)) 。 

由于 m≤n，所以时间复杂度是 O(log(min(m,n)))。空间复杂度：O(1)， 我们只需要恒定的内存来存储 9 个局部变量， 所以空间复杂度为 O(1)。


# 11. 盛最多水的容器
[Container With Most Water](https://leetcode-cn.com/problems/container-with-most-water/)

给定 n 个非负整数 a1，a2，…，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

示例:
```
输入: [1,8,6,2,5,4,8,3,7]
输出: 49
```

## 方法一：移动指针
装水的面积取决于两条线中较短的那条的长度和两条线之间横坐标的差值。
用双指针`left`、`right`，分别指向数组`height`的首尾。只有较短边会对盛水量造成影响，因此移动较短边的指针，并比较当前盛水量和当前最大盛水量。直至左右指针相遇。
`area`在宽度变小的情况下，只有可能高度变大，面积才有可能变大
如果`height[left]`长度小于`height[right]`，无论如何移动`right`，短板在`left`，不可能找到比当前记录的 `area` 更大的值了，只能通过移动`left`来找到新的可能的更大面积。这种方式可以遍历到容量最大的情况。

```python
class Solution:
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        if height == []:
            return 0
        
        left, right, area = 0, len(height)-1, 0 
        
        while left<right: 
            area = max(area, min(height[left], height[right]) * (right-left))
            
            if height[left]<height[right]:#area的高取决于左指针
                left+=1
            else: #area的高取决于右指针
                right-=1 
        return area
```
Time Complexity: O(n) O(n)O(n)

## 方法一改进
```python
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        l = 0
        r = len(height)-1
        s = 0
        
        while l < r:
            width = r-l
            
            if height[l] < height[r]:
                h = height[l]
                l += 1
            else:
                h = height[r]
                r -= 1
            s = max(s, h*width)
        return s
```

# 54. 螺旋矩阵
给定一个包含 m x n 个元素的矩阵（m 行, n 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。

示例 1:
```
输入:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
输出: [1,2,3,6,9,8,7,4,5]
```

## 方法

```python
class Solution:
    def spiralOrder(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: List[int]
        """
        
        if len(matrix)==0: return
        
        res = []
        
        # case 0,1,2,3分别表示按螺旋顺序要遍历的上、右、底、左边
        case = 0
        
        #尚未遍历的矩阵的四条边位置
        up = 0
        down = len(matrix)-1
        left = 0
        right = len(matrix[0])-1
        
        
        while left<=right and up<=down:
            if case == 0: #遍历上边
                for i in range(left, right+1):
                    res.append(matrix[up][i])
                up+=1
            elif case == 1:#遍历右边
                for i in range(up, down+1):
                    res.append(matrix[i][right])
                right -= 1
            elif case == 2: #遍历底边
                for i in range(left, right+1)[::-1]:
                    res.append(matrix[down][i])
                down -= 1
            else: #遍历左边
                for i in range(up, down+1)[::-1]:
                    res.append(matrix[i][left])
                left += 1
            case = (case + 1) % 4
        return res
```


# 59. 螺旋矩阵 II
给定一个正整数 n，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

示例:
```
输入: 3
输出:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]
```

## 方法
类似于上面54题

```python
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        if n == 0: return []
        res = [[0 for x in range(n)] for x in range(n)]
        cnt, case = 1, 0
        i, j = 0, 0
        weight = 1
        res[0][0] = 1
        while cnt < n * n:
            if case == 0:
                j += 1 #横向递增
                if j == n - weight:
                    case = 1
            elif case == 1:
                i += 1 #纵向递增
                if i == n - weight:
                    case = 2
            elif case == 2:
                j -= 1 #横向向左
                if j == weight - 1:
                    case = 3
                    weight += 1
            elif case == 3:
                i -= 1 #纵向向上               
                if i == weight - 1:
                    case = 0
            res[i][j] = cnt + 1
            cnt += 1
        return res
```

# 136. 只出现一次的数字
给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

示例 :
```
输入: [4,1,2,1,2]
输出: 4
```
## 方法一：异或位运算
异或运算是可以交换顺序的运算，也就是说和元素的排列顺序无关，自己异或自己等于0,0异或别人等于别人。
```python
from functools import reduce
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        return reduce(lambda x, y: x^y, nums)
```
## 方法二：字典
使用Counter直接求只出现一次的数字即可
```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        count = collections.Counter(nums)
        return count.most_common()[-1][0]
```


# 977. 有序数组的平方
给定一个按非递减顺序排序的整数数组 A，返回每个数字的平方组成的新数组，要求也按非递减顺序排序。

示例 1：
```
输入：[-4,-1,0,3,10]
输出：[0,1,9,16,100]
```
示例 2：
```
输入：[-7,-3,2,3,11]
输出：[4,9,9,49,121]
```
提示：

-   1 <= A.length <= 10000
-   -10000 <= A[i] <= 10000
-   A 已按非递减顺序排序。

## 方法一：归并排序
用两个指针分别读取数组的非负部分与负数部分 —— 指针 i 反向读取负数部分，指针 j 正向读取非负数部分。
```python
class Solution:
    def sortedSquares(self, A):
        """
        :type A: List[int]
        :rtype: List[int]
        """
        N = len(A)
        j = 0
        while A[j]<0 and j<N-1:
            j+=1
        i = j-1
        
        res = []
        while 0<=i and j<N: # 归并操作, 谁的平方小，谁先输出
            if A[i]**2 < A[j]**2:
                res.append(A[i]**2)
                i-=1
            else:
                res.append(A[j]**2)
                j+=1
        while i>=0: # 如果负数部分没有输出完，则直接输出
            res.append(A[i]**2)
            i-=1
        while j<N:  # 如果正数部分没有输出完，则直接输出
            res.append(A[j]**2)
            j+=1
        return res
```
Time Complexity: O(n) O(n)O(n)

## 方法二：排序
```python
class Solution:
    def sortedSquares(self, A):
        return sorted(x*x for x in A)
```        
Time Complexity: O(nlogn) O(nlogn)O(nlogn)


# 240. 搜索二维矩阵 II
编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：

每行的元素从左到右升序排列。
每列的元素从上到下升序排列。
示例:
```
现有矩阵 matrix 如下：
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
给定 target = 5，返回 true。

给定 target = 20，返回 false。
```

## 方法
这个题在剑指offer有详细解释。方法是从右上角向左下角进行遍历，根据比较的大小决定向下还是向左查找。

剑指offer的解释是我们从矩阵的左下角或者右上角开始遍历，这样知道了比较的结果是大还是小，就知道了对应的前进方向。
```python
class Solution:
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        if not matrix or not matrix[0]:
            return False
        rows = len(matrix)
        cols = len(matrix[0])
        row, col = 0, cols-1 #从右上角开始
        while True:
            if row < rows and col >= 0:
                if matrix[row][col] == target:
                    return True
                elif matrix[row][col] < target:
                    row += 1
                else:
                    col -= 1
            else:
                return False
```

# 74. 搜索二维矩阵
编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

每行中的整数从左到右按升序排列。
每行的第一个整数大于前一行的最后一个整数。
示例 1:
```
输入:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 3
输出: true
```
## 方法一
这个题目是240题的一个特例，所以可以直接使用240题的代码就能通过。方法是从矩阵的左下角或者右上角开始遍历。

这个题在剑指offer有详细解释。方法是从右上角向左下角进行遍历，根据比较的大小决定向下还是向左查找。

```python
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        if not matrix or not matrix[0]:
            return False
        rows = len(matrix)
        cols = len(matrix[0])
        row, col = 0, cols-1 #从右上角开始
        while True:
            if row < rows and col >= 0:
                if matrix[row][col] == target:
                    return True
                elif matrix[row][col] < target:
                    row += 1
                else:
                    col -= 1
            else:
                return False  
```

## 方法二：库函数
使用库函数也可以哦，不过库函数都是针对一维数组的查找，所以我们需要把给出的数组变成一维的。在numpy中有reshape函数，幸运的是，leetcode支持Numpy.
```python
import numpy as np
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        matrix = np.reshape(matrix, [1, -1])
        return target in matrix    
```

# 347. 前 K 个高频元素
给定一个非空的整数数组，返回其中出现频率前 k 高的元素。

示例 1:
```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```
示例 2:
```
输入: nums = [1], k = 1
输出: [1]
```
说明：

-   你可以假设给定的 k 总是合理的，且 1 ≤ k ≤ 数组中不相同的元素的个数。
-   你的算法的时间复杂度必须优于 O(n log n) , n 是数组的大小。

## 字典
这个题要求时间复杂度是O(nlogn)，就可以按照出现的次数先排个序，然后找到出现最多的k个就好。

Counter类有most_common()函数，能按出现的次数进行排序。返回的是个列表，列表中每个元素都是一个元组，元组的第一个元素是数字，第二个数字是出现的次数。

```python
from collections import Counter
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        counter = Counter(nums).most_common()
        return [counter[i][0] for i in range(k)]
```

# 560. 和为K的子数组
给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。

示例 1 :
```
输入:nums = [1,1,1], k = 2
输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。
```
说明 :

-   数组的长度为 [1, 20,000]。
-   数组中元素的范围是 [-1000, 1000] ，且整数 k 的范围是 [-1e7, 1e7]。

## 方法：字典
使用一个字典保存数组某个位置之前的数组和，然后遍历数组求和，这样当我们求到一个位置的和的时候，向前找sum-k是否在数组中，如果在的话，更新结果为之前的结果+(sum-k出现的次数)。同时，当前这个sum出现的次数就多了一次。

这个字典的意义是什么呢？其意义就是我们在到达i位置的时候，前i项的和出现的次数的统计。我们想找的是在i位置向前的连续区间中，有多少个位置的和是k。有了这个统计，我们就不用向前一一遍历找sum - k在哪些位置出现了，而是直接得出了前面有多少个区间。所以，在每个位置我们都得到了以这个位置为结尾的并且和等于k的区间的个数，所以总和就是结果。

这个题的解法不难想出来，因为如果要降低时间复杂度，应该能想到增加空间复杂度，那么要么使用数组，要么就是用字典之类的，保留之前的结果。

时间复杂度是O(N)，空间复杂度是O(N).

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        n = len(nums)
        d = collections.defaultdict(int)
        d[0] = 1
        res = 0
        sum = 0
        for i in range(n):
            sum += nums[i]
            if sum-k in d:
                res += d[sum-k]
            d[sum] += 1
        return res
```

# 参考
-   [LeetCode：977. Squares of a Sorted Array - Python](https://blog.csdn.net/xx_123_1_rj/article/details/86568877)
-   [leetcode11. Container With Most Water 盛水最多的容器](https://segmentfault.com/a/1190000008824222)