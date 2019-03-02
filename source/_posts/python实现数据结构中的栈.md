---
title: python实现数据结构中的栈
date: 2019-03-01
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 数据结构
        - 栈
---
栈是一种后入先出（LIFO，last-in-first-out）的数据结构。添加移除新项总发生在同一端。这一端通常称为“顶部”。与顶部对应的端称为“底部”。

栈的底部很重要，因为在栈中靠近底部的项是存储时间最长的。最近添加的项是最先会被移除的。

对栈的两种主要操作是将一个元素压入栈和将一个元素弹出栈。入栈使用`push()`方法，出栈使用`pop()`方法。


# 用数组实现一个顺序栈

假定数组的结尾作为栈顶，后端插入和删除是 O(1) 操作。

栈有如下基本操作：
- Stack() 创建一个空的新栈。 返回一个空栈。
- push(item)将一个新项添加到栈的顶部。它需要 item 做参数并不返回任何内容。
- pop() 从栈中删除顶部项。它返回 item 。栈被修改。
- top() 从栈返回顶部项，但不会删除它。不修改栈。
- isEmpty() 测试栈是否为空。返回布尔值。
- size() 返回栈中的 item 数量。返回一个整数。


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
- 需要完整的大块存储空间
- 扩大存储的操作代价高

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
# LeetCode 习题

## 有效的括号
[Valid Parentheses](https://leetcode-cn.com/problems/valid-parentheses/)

## 最长有效的括号
[Longest Valid Parentheses](https://leetcode-cn.com/problems/longest-valid-parentheses/)

## 逆波兰表达式求值
[Evaluate Reverse Polish Notatio](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

# 参考
- [problem-solving-with-algorithms-and-data-structure-using-python](https://facert.gitbooks.io/python-data-structure-cn/content/)
- [How does the Back button in a web browser work?
](https://stackoverflow.com/questions/1313788/how-does-the-back-button-in-a-web-browser-work)