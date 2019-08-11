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

-   54. 螺旋矩阵
-   59. 螺旋矩阵 II
<!-- more -->

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