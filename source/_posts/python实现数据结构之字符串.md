
---
title: python实现数据结构之字符串
date: 2019-03-08
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   字符串
mathjax: true
---
学习目标：

-   实现一个字符集，只包含 a～z 这 26 个英文字母的 Trie 树 
-   实现朴素的字符串匹配算法 
-   LeetCode 344. 反转字符串
-   LeetCode 151. 翻转字符串里的单词
-   LeetCode 8. 字符串转换整数 (atoi)

<!-- more -->

# LeetCode 208. 实现 Trie (前缀树)

实现一个 Trie (前缀树)，包含 insert, search, 和 startsWith 这三个操作。

示例:
```
Trie trie = new Trie();

trie.insert("apple");
trie.search("apple");   // 返回 true
trie.search("app");     // 返回 false
trie.startsWith("app"); // 返回 true
trie.insert("app");   
trie.search("app");     // 返回 true
```

说明:

你可以假设所有的输入都是由小写字母 a-z 构成的。

保证所有输入均为非空字符串。

## 方法
trie，又称前缀树或字典树，是一种有序树，用于保存关联数组，其中的键通常是字符串。

一个节点的所有子孙都有相同的前缀，也就是这个节点对应的字符串，而根节点对应空字符串。

```python
class TrieNode:
    def __init__(self):
        self.isEnd = False  # 判断是否是词尾
        self.children = dict() #子结点
        
class Trie:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        """
        Inserts a word into the trie.
        """
        currNode = self.root
        for i in word:
            if i not in currNode.children:
                currNode.children[i] = TrieNode()
            currNode = currNode.children[i]
        currNode.isEnd = True

    def search(self, word: str) -> bool:
        """
        Returns if the word is in the trie.
        """
        currNode = self.root 
        for i in word:
            if i not in currNode.children:
                return False
            currNode = currNode.children[i]
        return currNode.isEnd

    def startsWith(self, prefix: str) -> bool:
        """
        Returns if there is any word in the trie that starts with the given prefix.
        """
        currNode = self.root
        for i in prefix:
            if i not in currNode.children:
                return False
            currNode = currNode.children[i]
        return True

# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.search(word)
# param_3 = obj.startsWith(prefix)
```

## Trie树的优缺点

Trie树的核心思想是空间换时间，利用字符串的公共前缀来减少无谓的字符串比较以达到提高查询效率的目的。

优点

-   插入和查询的效率很高，都为O(m)O(m)，其中 mm 是待插入/查询的字符串的长度。

    -   关于查询，会有人说 hash 表时间复杂度是O(1)O(1)不是更快？但是，哈希搜索的效率通常取决于 hash 函数的好坏，若一个坏的 hash 函数导致很多的冲突，效率并不一定比Trie树高。
-   Trie树中不同的关键字不会产生冲突。

-   Trie树只有在允许一个关键字关联多个值的情况下才有类似hash碰撞发生。

-   Trie树不用求 hash 值，对短字符串有更快的速度。通常，求hash值也是需要遍历字符串的。

-   Trie树可以对关键字按字典序排序。

缺点

-   当 hash 函数很好时，Trie树的查找效率会低于哈希搜索。

空间消耗比较大。

# LeetCode 28. 实现strStr()
[Implement strStr()](https://leetcode-cn.com/problems/implement-strstr/)

实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1:
```
输入: haystack = "hello", needle = "ll"
输出: 2
```
示例 2:
```
输入: haystack = "aaaaa", needle = "bba"
输出: -1
```
说明:

当 needle 是空字符串时我们返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。

## 方法：内置find()函数

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        return haystack.find(needle)
```

## 方法：朴素算法
字符串匹配算法主要是两类，最基本的暴力解法，也叫做朴素算法，另一种是KMP算法。

朴素算法是通过一个循环找到所有有效偏移，检查是否满足条件。
```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        M, N = len(haystack), len(needle)
        for i in range(M-N+1):
            if haystack[i:i+N] == needle:
                return i
        return -1
```


# LeetCode 344. 反转字符串
[反转字符串](https://leetcode-cn.com/problems/reverse-string/)


编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 char[] 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 ASCII 码表中的可打印字符。

示例 1：
```
输入：["h","e","l","l","o"]
输出：["o","l","l","e","h"]
```
示例 2：
```
输入：["H","a","n","n","a","h"]
输出：["h","a","n","n","a","H"]
```
## 方法
依次交换前面和后面的字符直至中间字符，完成反转。
```python3
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        for i in range(len(s)//2):
            tmp = s[i]
            s[i] = s[len(s)-1-i]
            s[len(s)-1-i] = tmp
```

# LeetCode 151. 翻转字符串里的单词
[Reverse Words in a String](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

给定一个字符串，逐个翻转字符串中的每个单词。
 
示例 1：
```
输入: "the sky is blue"
输出: "blue is sky the"
```
示例 2：
```
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
```
示例 3：
```
输入: "a good   example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。
```

说明：

-   无空格字符构成一个单词。
-   输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
-   如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

## 方法
```python
class Solution:
    def reverseWords(self, s: str) -> str:
        lst = s.split()
        lst.reverse()
        return " ".join(lst)
```

# LeetCode 8. 字符串转换整数 (atoi)
[字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

请你来实现一个 atoi 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

说明：假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 $[−2^{31},  2^{31} − 1]$。如果数值超过这个范围，qing返回  INT_MAX$(2^{31} − 1)$ 或 INT_MIN $(−2^{31})$ 。

**示例 1:**

```
输入: "42"
输出: 42
```

**示例 2:**

```
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

**示例 3:**
```
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

**示例 4:**

```
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。
```
**示例 5:**
```
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−231) 。
```

## 方法一：按条件多次判断
按题目要求进行如下处理和判断：
1. strip()去掉首位空格字符
2. 如果字符串为空，返回0
3. 判断首字符是否为正负号
4. 遍历每个字符，若在0-9范围内，则加入数字中；否则break。
5. 乘上正负号之后，判断数字是否越界。

```python
class Solution:
    def myAtoi(self, str):
        """
        :type str: str
        :rtype: int
        """        
        str = str.strip()
        number, flag = 0, 1
        if not str:
            return 0
        
        if str[0] == '-' and len(str)>1:
            str = str[1:]
            flag = -1
        elif str[0] == '+' and len(str)>1:
            str = str[1:]
        for c in str:
            if c >= '0' and c <= '9':
                number = 10*number + ord(c) - ord('0')
            else:
                break
        number = flag * number
        number = number if number <= 2**31-1 else 2**31-1
        number = number if number >= -2**31 else -2**31
        return number
```

## 方法二：正则匹配
用正则表达式来匹配
```python
class Solution:
    def myAtoi(self, str):
        """
        :type str: str
        :rtype: int
        """
        # 导入正则模块
        import re
        # 字符串中查找全部符合条件的整数，返回的是列表，第一个参数是正则，第二个参数是字符串
        ret = re.findall(r"^[-+]?\d+", str.strip())  # strip()字符串去空格
        # 判断是否有匹配的值，没有的话返回0，例如"word values 987"，匹配不到，返回0
        if ret:
            ret_str = ret[0]  # 匹配的数字的字符串
            ret_str2 = ""  # 记录去符号的字符串，ret_str后面还要使用，所以定义一个新的变量记录
            # 判断是否带有符号 + or -
            if ret_str[0] == "-" or ret_str[0] == "+":
                ret_str2 = ret_str[1:]
            else:
                ret_str2 = ret_str
            # str转int
            ret_int = int(ret_str2)
            # 判断第一个字符是否为负号
            if ret_str[0] == "-":
                # 三目运算符，判断是否溢出
                # 如果ret_int <= 2**31则返回-ret_int，否则返回-2**31
                return -ret_int if ret_int <= 2**31 else -2**31
            else:
                return ret_int if ret_int < 2**31 else 2**31-1
        else:
            return 0
```
比方法一的执行时间短



# 参考
-   [Trie树（Prefix Tree）介绍](https://blog.csdn.net/lisonglisonglisong/article/details/45584721)