---
title: python刷leetcode字符串(1)
date: 2019-07-2
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   字符串
mathjax: true
---

-   38. 报数
-   58. 最后一个单词的长度
-   67. 二进制求和
-   125. 验证回文串
-   345. 反转字符串中的元音字母
-   383. 赎金信
<!-- more -->

# 38. 报数
报数序列是一个整数序列，按照其中的整数的顺序进行报数，得到下一个数。其前五项如下：
```
1.     1
2.     11
3.     21
4.     1211
5.     111221
```
1 被读作  "one 1"  ("一个一") , 即 11。

11 被读作 "two 1s" ("两个一"）, 即 21。

21 被读作 "one 2",  "one 1" （"一个二" ,  "一个一") , 即 1211。

给定一个正整数 n（1 ≤ n ≤ 30），输出报数序列的第 n 项。

注意：整数顺序将表示为一个字符串。

示例 1:
```
输入: 1
输出: "1"
```
示例 2:
```
输入: 4
输出: "1211"
```

## 方法
外循环遍历序数，内循环统计个数和连接字符串
```python
class Solution:
    def countAndSay(self, n: int) -> str:
        res = '1'
        
        for i in range(n-1):
            pre = res[0] # 初始化
            count = 1
            ans = '' # 对上一结果进行计数和拼接字符
            
            for j in range(1, len(res)):
                cur = res[j] # 遍历res中的字符来统计个数
                
                if cur == pre:
                    count +=1
                else:
                    ans = ans + str(count) + str(pre)
                    pre = cur
                    count = 1
            res = ans + str(count) + str(pre) # 拼接j循环的最后一个字符
        return res                       
```

# 58. 最后一个单词的长度
给定一个仅包含大小写字母和空格 ' ' 的字符串，返回其最后一个单词的长度。

如果不存在最后一个单词，请返回 0 。

说明：一个单词是指由字母组成，但不包含任何空格的字符串。

示例:

输入: "Hello World"
输出: 5

## 方法：库函数

```python
class Solution:
    def lengthOfLastWord(self, s: str) -> int:
        return len(s.strip().split(' ')[-1])
```

# 67. 二进制求和
给定两个二进制字符串，返回他们的和（用二进制表示）。

输入为非空字符串且只包含数字 1 和 0。

示例 1:
```
输入: a = "11", b = "1"
输出: "100"
```

示例 2:
```
输入: a = "1010", b = "1011"
输出: "10101"
```

## 方法：进制转换函数
先将两个加数转成10进制，再把和转换成二进制返回即可。虽然速度还挺快的，但这么做忽略了可能的大整数相加的细节（因为Python帮你处理了）。

```python
class Solution:
    def addBinary(self, a: str, b: str) -> str:
        return bin(int(a,2) + int(b,2))[2:]
```

# 125. 验证回文串
给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

说明：本题中，我们将空字符串定义为有效的回文串。

示例 1:
```
输入: "A man, a plan, a canal: Panama"
输出: true
```
示例 2:
```
输入: "race a car"
输出: false
```

## 方法：列表生成式
isalnum()能判断字符是不是字母数字的，用列表表达式就能产生有效字符串，然后就可以看是不是回文。
```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        isvalid = lambda x : x == x[::-1]
        string = ''.join([x for x in s.lower() if x.isalnum()])
        return isvalid(string)
```

## 方法：正则表达式
替换非字母数字字符
```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        s = s.lower()
        s = re.sub('\W', '', s) 
        # 正则 sub替换, \W匹配任何非单词字符。等价于 '[^A-Za-z0-9_]'。
        N = len(s)
        left, right = 0, N-1
        
        while left <= right: 
            if s[left] != s[right]:
                return False
            left += 1
            right -= 1
        return True
```

# 345. 反转字符串中的元音字母
编写一个函数，以字符串作为输入，反转该字符串中的元音字母。

示例 1:
```
输入: "hello"
输出: "holle"
```
示例 2:
```
输入: "leetcode"
输出: "leotcede"
```
说明:
元音字母不包含字母"y"。

## 方法：栈
这个题的意思是把收尾向中间走的时候遇到的所有元音字符换位置。也就是说 “abecui”–>“ibucea”;

把某个东西进行翻转，很容易想到栈。所以把元音字符进栈，再次遍历的时候遇到元音字符就出栈即可。
```python
class Solution:
    def reverseVowels(self, s: str) -> str:
        vstack = []
        
        for c in s:
            if c in "aeiouAEIOU":
                vstack.append(c)
        
        res = []
        for c in s:
            if c in "aeiouAEIOU":
                res.append(vstack.pop())
            else:
                res.append(c)
        return ''.join(res)
```

# 383. 赎金信
给定一个赎金信 (ransom) 字符串和一个杂志(magazine)字符串，判断第一个字符串ransom能不能由第二个字符串magazines里面的字符构成。如果可以构成，返回 true ；否则返回 false。

(题目说明：为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思。)

注意：

你可以假设两个字符串均只含有小写字母。
```
canConstruct("a", "b") -> false
canConstruct("aa", "ab") -> false
canConstruct("aa", "aab") -> true
```

## 方法
直接Counter，然后判断前者的每个字符出现次数都小于后者即可。
```python
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        rcount = collections.Counter(ransomNote)
        mcount = collections.Counter(magazine)
        for r, c in rcount.items():
            if c > mcount[r]:
                return False
        return True
```