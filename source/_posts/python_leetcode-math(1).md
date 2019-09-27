---
title: python解LeetCode数学题(1)
date: 2019-04-22
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 牛客
mathjax: true
---
-   6. Z 字形变换
-   7. 整数反转
-   8. 字符串转换整数 (atoi)
    -   (解法见 [python实现数据结构之字符串](https://cathy3.github.io/2019/03/08/python%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%8B%E5%AD%97%E7%AC%A6%E4%B8%B2/))
-   9. 回文数
-   13. 罗马数字转整数
-   89. 格雷编码
-   204. 计数素数
-   231. 2的幂
-   263. 丑数
-   264. 丑数 II
-   292. Nim 游戏
-   400. 第N个数字
-

<!-- more -->

# 6. Z 字形变换
将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：
```
L   C   I   R
E T O E S I I G
E   D   H   N
之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："LCIRETOESIIGEDHN"。
```
请你实现这个将字符串进行指定行数变换的函数：
```
string convert(string s, int numRows);
```
示例 1:
```
输入: s = "LEETCODEISHIRING", numRows = 3
输出: "LCIRETOESIIGEDHN"
```

示例 2:
```
输入: s = "LEETCODEISHIRING", numRows = 4
输出: "LDREOEIIECIHNTSG"
解释:
L     D     R
E   O E   I I
E C   I H   N
T     S     G
```

# 方法：公式
把一个字符串按照锯齿型的方式去写，然后按照行进行拼接到一起

(1)第一行和最后一行下标间隔都是interval = n*2-2 = 8 ;

(2)中间行的间隔是周期性的,第i行的间隔是: interval–2*i, 2*i, interval–2*i, 2*i, interval–2*i, 2*i, …

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1: return s
        ans = ''
        
        interval = 2 * numRows -2
        for i in range(0, len(s), interval): #第一行
            ans += s[i]
        
        for row in range(1, numRows-1): #中间行
            inter = 2 * row
            i = row
            while i<len(s):
                ans += s[i]
                inter = interval - inter # inter重置，交替间隔两个公式
                i += inter
        
        for i in range(numRows-1, len(s), interval): # 最后一行
            ans += s[i]
        return ans
```


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

## 方法：从后往前遍历
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

# 89.格雷编码
格雷编码是一个二进制数字系统，在该系统中，两个连续的数值仅有一个位数的差异。

给定一个代表编码总位数的非负整数 n，打印其格雷编码序列。格雷编码序列必须以 0 开头。

示例 1:
```
输入: 2
输出: [0,1,3,2]
解释:
00 - 0
01 - 1
11 - 3
10 - 2

对于给定的 n，其格雷编码序列并不唯一。
例如，[0,2,3,1] 也是一个有效的格雷编码序列。

00 - 0
10 - 2
11 - 3
01 - 1
```

## 方法
下面是百度百科告诉的如何求n位的格雷码的方法。

递归生成码表

这种方法基于格雷码是反射码的事实，利用递归的如下规则来构造：
```
1位格雷码有两个码字
(n+1)位格雷码中的前2n个码字等于n位格雷码的码字，按顺序书写，加前缀0
(n+1)位格雷码中的后2n个码字等于n位格雷码的码字，按逆序书写，加前缀1
n+1位格雷码的集合 = n位格雷码集合(顺序)加前缀0 + n位格雷码集合(逆序)加前缀1
```
简言之就是递归。第（n+1）位的格雷码序列=（‘0’+第n位的正序） + （‘1’+第n位的逆序）

题目中说了n是非负数，当n=0的时候，返回[0]即可。

```python
class Solution:
    def grayCode(self, n: int) -> List[int]:
        return map(lambda x: int(x,2), self.bit_gray(n))

    def bit_gray(self,n):
        if n==0: return ["0"]
        elif n==1: return ["0","1"]
        else:
            pre_gray = self.bit_gray(n-1)
            return ["0"+x for x in pre_gray] + ["1"+x for x in pre_gray[::-1]]
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

# 231. 2的幂
给定一个整数，编写一个函数来判断它是否是 2 的幂次方。

示例 1:
```
输入: 1
输出: true
解释: 20 = 1
```
示例 2:
```
输入: 16
输出: true
解释: 24 = 16
```

## 方法一：二进制
数一下二进制中1的个数是不是正好是1个

```python
class Solution:
    def isPowerOfTwo(self, n: int) -> bool:
        return n>0 and bin(n).count("1")==1
```

## 方法二：位运算
n&(n-1) 的方法来数一个数里边只有一个1
```python
class Solution(object):
    def isPowerOfTwo(self, n):
        """
        :type n: int
        :rtype: bool
        """
        if n <= 0: return False
        return n & (n - 1) == 0
```


# 263. 丑数
编写一个程序判断给定的数是否为丑数。

丑数就是只包含质因数 2, 3, 5 的正整数。

示例 1:
```
输入: 6
输出: true
解释: 6 = 2 × 3
```
示例 2:
```
输入: 8
输出: true
解释: 8 = 2 × 2 × 2
```
示例 3:
```
输入: 14
输出: false 
解释: 14 不是丑数，因为它包含了另外一个质因数 7。
```
说明：

-   1 是丑数。
-   输入不会超过 32 位有符号整数的范围: [−231,  231 − 1]。

## 方法
把这个数中的所有的2,3,5的因子全部除去，剩余的数为1，则说明全部为这几个因子构成。
```python
class Solution:
    def isUgly(self, num: int) -> bool:
        if num==0: return False
        while num%2==0:
            num /= 2
        while num%3==0:
            num /= 3
        while num%5==0:
            num /= 5
        return num==1
```

# 264. 丑数 II
编写一个程序，找出第 n 个丑数。

丑数就是只包含质因数 2, 3, 5 的正整数。

示例:
```python
输入: n = 10
输出: 12
解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
```
说明:  

-   1 是丑数。
-   n 不超过1690。

## 方法
```python
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        if n<0: return 0
        dp = [1]*n
        index2, index3, index5 = 0,0,0
        for i in range(1,n):
            dp[i] = min(dp[index2]*2, dp[index3]*3, dp[index5]*5)
            if dp[i]==dp[index2]*2: index2 += 1
            if dp[i]==dp[index3]*3: index3 += 1
            if dp[i]==dp[index5]*5: index5 += 1
        return dp[-1]
```

# 292. Nim 游戏
你和你的朋友，两个人一起玩 Nim 游戏：桌子上有一堆石头，每次你们轮流拿掉 1 - 3 块石头。 拿掉最后一块石头的人就是获胜者。你作为先手。

你们是聪明人，每一步都是最优解。 编写一个函数，来判断你是否可以在给定石头数量的情况下赢得游戏。

示例:
```
输入: 4
输出: false 
解释: 如果堆中有 4 块石头，那么你永远不会赢得比赛；
     因为无论你拿走 1 块、2 块 还是 3 块石头，最后一块石头总是会被你的朋友拿走。
```

## 方法
题目的意思是只要拿最后一个石子的人赢。

因为每次最多拿三个，所以只要我拿走子之后，最后剩余四个的话，我就输了。

所以，所有子的个数不能被四整除我就赢了，否则我会输。输的原因是对手每次都拿4-n，n为当次我拿到子的个数。
```python
class Solution:
    def canWinNim(self, n: int) -> bool:
        return n % 4 != 0
```

# 400. 第N个数字
在无限的整数序列 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...中找到第 n 个数字。

注意:n 是正数且在32为整形范围内 ( n < 231)。

示例 1:
```
输入:3
输出:3
```
示例 2:
```
输入:11
输出:0
```
说明: 第11个数字在序列 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... 里是0，它是10的一部分。

# 方法
找规律：个位数字有9个，2位数字有910=90个，3位数字有9100=900个……所以我们先求出n是几位数字，然后判断第n个数字应该落在哪个自然数上，最后再求这个自然数会落在自然数的那一位上。
```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        _len = 1
        count = 9
        start = 1
        while n > count*_len:
            n -= count*_len
            count *= 10
            _len += 1
            start *= 10
        start += (n-1)/_len
        return int(str(start)[(n-1)%_len])
```

# 参考
-   [How Many Primes Are There?](https://primes.utm.edu/howmany.html)
-   [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)