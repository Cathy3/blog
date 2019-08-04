---
title: python刷LeetCode数学题(一)
date: 2019-04-22
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 牛客
mathjax: true
---
-   7. 整数反转
-   8. 字符串转换整数 (atoi)
    -   (解法见 [python实现数据结构之字符串](https://cathy3.github.io/2019/03/08/python%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%8B%E5%AD%97%E7%AC%A6%E4%B8%B2/))
-   9. 回文数
-   13. 罗马数字转整数
-   204. 计数素数


<!-- more -->

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

## 方法一:取模
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

# 9. 回文数
判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:
```
输入: 121
输出: true
```
示例 2:
```
输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```
示例 3:
```
输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
```

## 方法一：转成字符串
```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0: return False
        x = str(x)
        N = len(x)
        for i in range(N//2):
            if x[i] != x[N-1-i]:
                return False
        return True
```

## 方法二：数学除余+取模
借助一个与x同等位数的变量`help`（首位1，其余位0），通过循环：
1. 找到`x`的最高位`x//help`和最低位`x%10`，进行比较，
2. 去掉`x`两头的数字`(x%help)//10`。help变量同步修改去掉两个0。 `help/=100`。

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0: return False
        
        help=1
        while x//help >= 10:
            help *= 10
        
        while x!=0:
            if x//help != x%10:
                return False
            x = (x%help)//10
            help /= 100
        return True
```

# 13. 罗马数字转整数
罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。
```
字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```
例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

-   I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
-   X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
-   C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。

给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

示例 1:
```
输入: "III"
输出: 3
```
示例 2:
```
输入: "IV"
输出: 4
```
示例 3:
```
输入: "IX"
输出: 9
```
示例 4:
```
输入: "LVIII"
输出: 58
解释: L = 50, V= 5, III = 3.
```
示例 5:
```
输入: "MCMXCIV"
输出: 1994
解释: M = 1000, CM = 900, XC = 90, IV = 4.
```

# 方法：从后往前遍历
```python
class Solution:
    def romanToInt(self, s: str) -> int:
        roman = {"I":1, "V":5, "X":10, "L":50, "C":100, "D":500, "M":1000}
        res = roman[s[-1]]
        N = len(s)
        for i in range(N-2, -1, -1):
            if roman[s[i]] < roman[s[i+1]]:
                res -= roman[s[i]]
            else:
                res += roman[s[i]]
        return res
```


# 204. 计数素数
质数（prime number）又称素数，有无限个。 质数定义为在大于1的自然数中，除了1和它本身以外不再有其他因数。

## 题目描述
统计所有小于非负整数 n 的质数的数量。

示例:
```
输入: 10
输出: 4
解释: 小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
```

## 方法：素数筛法
素数筛法就是把这个数的所有倍数都删除掉，因为这些数一定不是素数。最后统计一下数字剩余的没有被删除的个数就好。
```python
class Solution:
    def countPrimes(self, n: int) -> int:
        nums = [1]*n
        for i in range(2, n):
            if nums[i]==0:
                continue # 筛掉不是素数的值
            j = 2
            while i*j < n:
                nums[i*j] = 0
                j += 1
        res = 0
        for i in range(2, n):
            res += nums[i]
        return res
```
执行用时有点长。




# 参考
-   [How Many Primes Are There?](https://primes.utm.edu/howmany.html)
-   [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)