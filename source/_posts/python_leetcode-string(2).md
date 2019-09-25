---
title: python解leetcode字符串(2)
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
-   3. 无重复字符的最长子串
-   5. 最长回文子串
-   14. 最长公共前缀
-   38. 报数
-   43. 字符串相乘
-   58. 最后一个单词的长度
-   67. 二进制求和
-   125. 验证回文串
-   345. 反转字符串中的元音字母
-   383. 赎金信

<!-- more -->

# 3. 无重复字符的最长子串
给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:
```
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```
示例 2:
```
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```
示例 3:
```
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

## 方法：双指针, sliding window
[left, right]双闭区间来保存子串的左右区间，对应着这个区间我们维护一个set，这个set里面全部是不重复的字符。

使用while循环，如果right字符不在set中，就让它进去；如果right在，就把left对应的字符给remove出去。

所以，当我们得到一个right位置的字符时，通过移动left和修改[left,right]区间内对应的的set，来保持了一个最小的不重复字符区间。这里需要注意的是，移动left的次数不一定就是1次，因为我们要保证left和right之间没有重复字符，而新添加的right字符出现的位置不一定刚刚就是left指向的位置。

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        l = len(s)
        left, right = 0, 0 
        chars = set()
        res = 0
        while left < l and right < l:
            if s[right] not in chars:
                chars.add(s[right])
                right += 1
                res = max(res, len(chars))
            else:
                if s[left] in chars:
                    chars.remove(s[left])
                left += 1
        return res
```


# 5. 最长回文子串
[最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

示例 1：
```
输入: “babad”
输出: “bab”
注意: “aba” 也是一个有效答案。
```
示例 2：
```
输入: “cbbd”
输出: “bb”
```

## 方法：马拉车算法
马拉车算法(Manacher’s algorithm)是用来查找一个字符串的最长回文子串的线性方法，由一个叫Manacher的人在1975年发明的，这个方法的最大贡献是在于将时间复杂度提升到了线性，这是非常了不起的。

思路：

1.  对字符串进行预处理，两个字符之间加上特殊符号 #
2.  用一个列表 P 来记录以每个字符 i 为中心的回文长度，
3.  遍历整个字符串：
    1.  比较 若 i 小于最右边界：那就找它相对i的对称位置，得出回文长度，要是对称位置的回文长度更长，则选择更小的 右边界-i,
    2.  否则 P[i] 就取为0
    3.  +1循环，扩展探索 i 的边界
    4.  如果它的边界超过右边界，则取而代之
4.  最后取得最长回文。

```python
class Solution:
    def longestPalindrome(self, s):
        """
        :type s: str
        :rtype: str
        """
        def preProcess(s):
            if not s:
                return ['^', '$']
            T = ['^']
            for c in s:
                T += ['#', c]
            T += ['#', '$']
            return T
        
        T = preProcess(s)
        P = [0] * len(T)
        center, right = 0, 0
        for i in range(1, len(T)-1):
            i_mirror = 2 * center-i
            if right > i:
                P[i] = min(right-i, P[i_mirror])
            else:
                P[i] = 0
            
            while T[i + 1 + P[i]] == T[i - 1 - P[i]]:
                P[i] += 1
            
            if i + P[i] > right:
                center, right = i, i+P[i]
        max_i = 0
        for i in range(1, len(T)-1):
            if P[i] > P[max_i]:
                max_i = i
        start = round((max_i - 1 - P[max_i]) / 2)
        return s[start : start + P[max_i]]
```
Time Complexity: O(n)

Auxiliary Space: O(n) 


# 14. 最长公共前缀
编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 “”。

示例 1:
```
输入: [“flower”,“flow”,“flight”]
输出: “fl”
```
示例 2:
```
输入: [“dog”,“racecar”,“car”]
输出: “”
解释: 输入不存在公共前缀。
```
说明:所有输入只包含小写字母 a-z 。

## 方法：用 zip() 和 set() 函数
如果`set()`的长度大于1，则返回当前的最长公共前缀。`enumerate(zip(*strs))` 返回每个单词的字符序号和元组。

```python
class Solution:
    def longestCommonPrefix(self, strs):
        """
        :type strs: List[str]
        :rtype: str
        """
        if not strs:
            return ""
        for i, letter_group in enumerate(zip(*strs)):
            if len(set(letter_group)) > 1:
                return strs[0][:i]
        return min(strs)
```
Time Complexity: O(n) 

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

# 39. 组合总和
给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的数字可以无限制重复被选取。

说明：

1.  所有数字（包括 target）都是正整数。
2.  解集不能包含重复的组合。 

示例 1:
```
输入: candidates = [2,3,6,7], target = 7,
所求解集为:
[
  [7],
  [2,2,3]
]
```
示例 2:
```
输入: candidates = [2,3,5], target = 8,
所求解集为:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```
## 方法：dfs
使用候选集的数字，能有多少种不同的组合，使得每个组合的和都是target。

dfs搜索，先对所有的值进行排序，然后对每个元素进行dfs搜索，判断能否得到target。用了几个条件判断进行提前终止，这样能加速。

```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        res = []
        candidates.sort()
        self.dfs(candidates, target, 0, res, [])
        return res
    
    def dfs(self, nums, target, index, res, path):
        if target < 0:
            return 
        elif target == 0:
            res.append(path)
            return
        for i in range(index, len(nums)):
            if nums[index] > target:
                return
            self.dfs(nums, target-nums[i], i, res, path+[nums[i]])
```

# 43. 字符串相乘
给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。

示例 1:
```
输入: num1 = “2”, num2 = “3”
输出: “6”
```
示例 2:
```
输入: num1 = “123”, num2 = “456”
输出: “56088”
```
说明：

-   num1 和 num2 的长度小于110。
-   num1 和 num2 只包含数字 0-9。
-   num1 和 num2 均不以零开头，除非是数字 0 本身。
-   不能使用任何标准库的大数类型（比如 BigInteger）或直接将输入转换为整数来处理。

## 方法
按乘法运算的方式来实现

```python
class Solution:
    def multiply(self, num1: str, num2: str) -> str:
        res = [0] * (len(num1) + len(num2))  # 初始化，存放乘积的数组
        pos = len(res) - 1

        for n1 in reversed(num1):
            tempPos = pos
            for n2 in reversed(num2):
                res[tempPos] += int(n1) * int(n2)
                res[tempPos - 1] += res[tempPos] // 10  # 进位
                res[tempPos] %= 10  # 取余
                tempPos -= 1
            pos -= 1

        st = 0
        while st < len(res) - 1 and res[st] == 0:  # 统计前面有几个0
            st += 1
        return ''.join(map(str, res[st:]))  # 去掉0，变成字符串
```

# 49. 字母异位词分组
给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

示例:
```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```
说明：

-   所有输入均为小写字母。
-   不考虑答案输出的顺序。

## 方法：字典
```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        res = {}
        for item in strs:
            k = ''.join(sorted(item))
            if k not in res:
                res[k] = []
            res[k].append(item) # 相同字符串放到同一个字典k中
        return [res[x] for x in res]
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

# 参考
-   [LeetCode003:最长回文字符-老表Pro](https://blog.csdn.net/qq_39241986/article/details/82927399)
