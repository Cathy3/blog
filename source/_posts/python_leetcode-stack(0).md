---
title: python实现数据结构中的栈
date: 2019-03-01
categories: 
		-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   栈
---
栈是一种数据呈线性排列的、后入先出（LIFO，last-in-first-out）的数据结构。不过在这种结构中，我们只能访问最新添加的数据。添加移除新项总发生在同一端。这一端通常称为“顶部”。与顶部对应的端称为“底部”。

栈的底部很重要，因为在栈中靠近底部的项是存储时间最长的。最近添加的项是最先会被移除的。

对栈的两种主要操作是将一个元素压入栈和将一个元素弹出栈。入栈使用`push()`方法，出栈使用`pop()`方法。

在栈的应用上，比如深度优先搜索算法，通常会选择最新的数据作为候补顶点。在候补顶点的管理上就可以使用栈。 

学习目标：

-   用数组实现一个顺序栈
-   用链表实现一个链式栈
-   编程模拟实现一个浏览器的前进、后退功能
-   LeetCode 20. 有效的括号
-   LeetCode 32. 最长有效的括号
-   LeetCode 150. 逆波兰表达式求值

<!-- more -->

# 用数组实现一个顺序栈

假定数组的结尾作为栈顶，后端插入和删除是 O(1) 操作。

栈有如下基本操作：
-   Stack() 创建一个空的新栈。 返回一个空栈。
-   push(item)将一个新项添加到栈的顶部。它需要 item 做参数并不返回任何内容。
-   pop() 从栈中删除顶部项。它返回 item 。栈被修改。
-   top() 从栈返回顶部项，但不会删除它。不修改栈。
-   isEmpty() 测试栈是否为空。返回布尔值。
-   size() 返回栈中的 item 数量。返回一个整数。


```python
class SStack:
    def __init__(self):
        self.items = []
    def isEmpty(self):
        return self.items == []
        # return not self.items
    def push(self,item):
        self.items.append(item)
    def pop(self):
        return self.items.pop()
    def top(self):
        if self.items:
            return self.items[-1]
        else:
            return None
    def size(self):
        return len(self.items)
    
    
if __name__ == "__main__":
    s = SStack()
    s.push('a')
    print(s.top())
    s.push('b')
    s.push('c')
    print(s.pop())
```
输出：
```
a
c
```
数组实现的缺点：

-   需要完整的大块存储空间
-   扩大存储的操作代价高

而用链表实现就没有以上的问题。

# 用链表实现一个链式栈
对于链接表，将表头作为栈顶，在前端插入和删除都是 O(1) 操作。
```python
class Node: # 链表的结点
    def __init__(self, x, next_=None):
        self.val = x
        self.next = next_

class LStack: #栈的链接表实现
    def __init__(self):
        self._top = None
    def isEmpty(self):
        return self._top is None
    def push(self,item):
        self._top = Node(item, self._top)
    def pop(self):
        p = self._top
        self._top = p.next
        return p.val
    def top(self):
        if self._top:
            return self._top.val
        else:
            return None
    
    
if __name__ == "__main__":
    s = LStack()
    s.push('a')
    print(s.top())
    s.push('b')
    s.push('c')
    print(s.pop())
```
输出：
```
a
c
```

# 编程模拟实现一个浏览器的前进、后退功能
每个 web 浏览器都有一个返回按钮。当你浏览网页时，这些网页被放置在一个栈中（实际是网页的网址）。你现在查看的网页在顶部，你第一个查看的网页在底部。如果按‘返回’按钮，将按相反的顺序浏览刚才的页面。

```python
class Page: # 一个网页
    def __init__(self, url, prev_=None, next_=None):
        self.val = url
        self.next = next_
        self.prev = prev_

class History: 
    def __init__(self): 
        self._cur = Page(None)
    
    def loadPage(self, newPage): #加载新的页面
        p = Page(newPage,self._cur,None)
        self._cur.next = p
        self._cur = self._cur.next
        return self._cur.val
        
    def back(self): #返回上一页面
        if self._cur.prev is None:
            return
        self._cur = self._cur.prev
        return self._cur.val
    
    def forward(self): #前进页面
        if self._cur.next is None:
            return
        self._cur = self._cur.next
        return self._cur.val
    
if __name__ == '__main__':
    h = History()
    print('加载页面'+ h.loadPage('a')) 
    print('加载页面'+ h.loadPage('b'))
    print('返回到'+ h.back())       
    print('加载页面'+ h.loadPage('c')) 
    print('返回到'+ h.back())
    print('前进到'+ h.forward())    
```
输出：
```
加载页面a
加载页面b
返回到a
加载页面c
返回到a
前进到c
```

# LeetCode 20. 有效的括号
[Valid Parentheses](https://leetcode-cn.com/problems/valid-parentheses/)

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

-   左括号必须用相同类型的右括号闭合。
-   左括号必须以正确的顺序闭合。
-   注意空字符串可被认为是有效字符串。

示例 1:
```
输入: "()[]{}"
输出: true
```
示例 2:
```
输入: "([)]"
输出: false
```
示例 3:
```
输入: "{[]}"
输出: true 
```

## 方法
-   先建立一个map
-   遍历字符串，对输入的字符串入栈操作（如果入栈的元素是key的话）
-   依次比较，直到出现不匹配或者栈里所有元素都比较结束(栈空)。
-   还要注意这样的问题：如果最后多余了’key‘，比如()[]{}(，所以最后还要判断一下`len(stack)==0`

```python
class Solution:
    def isValid(self, s):
        """
        :type s: str
        :rtype: bool
        """
        stack = list()
        match = {'{':'}', '[':']', '(':')'}
        for i in s:
            if i in match:
                stack.append(i)
            else:
                if len(stack) == 0:
                    return False

                if match[stack[-1]] != i:
                    return False
                
                stack.pop()

        if len(stack) != 0:
            return False
        return True
```

# LeetCode 32. 最长有效的括号
[Longest Valid Parentheses](https://leetcode-cn.com/problems/longest-valid-parentheses/)

给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。

示例 1:
```
输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"
```
示例 2:
```
输入: ")()())"
输出: 4
解释: 最长有效括号子串为 "()()"
```

## 方法
-   用一个栈来存储左括号的索引.
-   遇到正确匹配的括号则弹出匹配的索引，所以栈中存储的是未匹配上的左括号。
-   新匹配上的括号位置减去前一段未匹配到的括号的索引的差，是当前有效子串的大小。

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        maxlen = 0  # 最长的字串长度
        last = -1   # 上一段有效子串的结尾位置
        stack = []  # 栈里存放左括号的位置序号
        
        for i in range(len(s)):
            if s[i] == '(':
                stack.append(i) # 存入左括号的位置序号
            else:# 右括号
                if stack == []: # 栈空
                    last = i # 配对失败，一段有效子串结尾
                else:
                    stack.pop() #取出配对左括号
                    if stack == []: # 配对完，栈空
                    #当前位置i减去上一段结尾last，是这一段有效子串的长度
                        maxlen = max(maxlen, i-last) 
                    else:
                        # stack剩余的左括号未必能配对成功，先比较当前的有效子串
                        maxlen= max(maxlen, i - stack[-1])
        return maxlen
```

# LeetCode 150. 逆波兰表达式求值
[Evaluate Reverse Polish Notatio](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

根据[逆波兰表示法](https://baike.baidu.com/item/%E9%80%86%E6%B3%A2%E5%85%B0%E5%BC%8F/128437)，求表达式的值。

有效的运算符包括 +, -, *, / 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。

说明：

-   整数除法只保留整数部分。
-   给定逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 0 的情况。

示例 1：
```
输入: ["2", "1", "+", "3", "*"]
输出: 9
解释: ((2 + 1) * 3) = 9
```

示例 2：
```
输入: ["4", "13", "5", "/", "+"]
输出: 6
解释: (4 + (13 / 5)) = 6
```

## 方法

-   逆波兰式（Reverse Polish notation，RPN，或逆波兰记法），也叫后缀表达式（将运算符写在操作数之后）
-   python 有个函数eval()，可以给它一个运算表达式，直接给你求值。中缀表达式转正常表达式很简单了，直接用栈就行。
-   但需要注意的是，python中的’/’负数除法和c语言不太一样。在python中，(-1)/2=-1，而在c语言中，(-1)/2=0。也就是c语言中，除法是向零取整，即舍弃小数点后的数。而在python中，是向下取整的。而这道题的oj是默认的c语言中的语法，所以需要在遇到’/’的时候注意一下。
-   可采用的方式是使用operator.truediv(int(a), int(b))变成和c相同的方式。

```python
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        stack = []
        operators = ['+', '-','*','/']
        
        for token in tokens:
            if token not in operators:
                stack.append(token)
            else:
                b = stack.pop()
                a = stack.pop()
                if token == '/':
                    res = int(operator.truediv(int(a), int(b)))
                else:
                    res = eval(a+token+b)
                stack.append(str(res))
        return int(stack.pop())
```



# 参考
-   [problem-solving-with-algorithms-and-data-structure-using-python](https://facert.gitbooks.io/python-data-structure-cn/content/)
-   [How does the Back button in a web browser work?
](https://stackoverflow.com/questions/1313788/how-does-the-back-button-in-a-web-browser-work)
-   [【LeetCode】150. Evaluate Reverse Polish Notation 解题报告（Python）](https://blog.csdn.net/fuxuemingzhu/article/details/79559703)