---
title: python实现排序算法
date: 2019-08-19
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   排序
mathjax: true
---
学习目标：

-   179. 最大数

<!-- more -->

# 179. 最大数
给定一组非负整数，重新排列它们的顺序使之组成一个最大的整数。

示例 1:
```
输入: [10,2]
输出: 210
```
示例 2:
```
输入: [3,30,34,5,9]
输出: 9534330
说明: 输出结果可能非常大，所以你需要返回一个字符串而不是整数。
```

## 方法
如果AB>BA，那么我们规定A>B，也就是最后拼接时A放在B之前.

注意的是可能参数是多个0，此时字符串拼接的结果是一串0，不符合常规的表达方法，要将其改为0。
```python
from functools import cmp_to_key
class Solution:
    def largestNumber(self, nums: List[int]) -> str:
        sorted_nums = sorted(map(str, nums), key=cmp_to_key(lambda x, y: int(y + x) - int(x + y)))
        result = ''.join(sorted_nums).lstrip('0')
        return result or '0'
```