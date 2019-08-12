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

-   11. 盛最多水的容器
-   54. 螺旋矩阵
-   59. 螺旋矩阵 II
-   977. 有序数组的平方
<!-- more -->

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

# 参考
-   [LeetCode：977. Squares of a Sorted Array - Python](https://blog.csdn.net/xx_123_1_rj/article/details/86568877)
-   [leetcode11. Container With Most Water 盛水最多的容器](https://segmentfault.com/a/1190000008824222)