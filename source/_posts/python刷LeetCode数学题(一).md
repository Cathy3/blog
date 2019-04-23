---
title: python刷LeetCode数学题(一)
date: 2019-04-22
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
---

# 7. 整数反转
给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

示例 1:
```
输入: 123
输出: 321
```
 示例 2:
```
输入: -123
输出: -321
```
示例 3:
```
输入: 120
输出: 21
```
注意:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

# 方法一:取模
循环通过对10取模得到尾部数字，一步步乘10构造新的翻转后的整数即可。然而要注意首先判断原数字的正负，最后还要判断结果是否溢出。

```python
class Solution:
    def reverse(self, x: int) -> int:
        flag = 1 if x>0 else -1
        rev = 0 
        x = abs(x)
        while(x!=0):
            rev = rev*10 + x%10
            x //= 10
        return rev*flag if rev < 2147483648 else 0
```

## 方法二：转字符串
整数符号提取出来，把整数转化为字符串，然后使用字符串的翻转。
```python
class Solution:
    def reverse(self, x: int) -> int:
        flag = -1 if x < 0 else 1
        x = int(str(abs(x))[::-1])
        return x*flag if x<=2 ** 31 -1 else 0
```