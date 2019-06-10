---
title: 用python刷LeetCode链表题(中等级)
date: 2019-04-05
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 链表
---
-   2. 两数相加
-   19. 删除链表的倒数第N个节点
-   24. 两两交换链表中的节点
-   61. 
-   82. 
-   86. 
-   92. 
-   109. 
-   138. 
-   142. 
-   143. 
-   147. 
-   148. 
-   328.    
-   369. 
-   379. 

<!-- more -->
# 2. 两数相加
给出两个**非空**的链表用来表示两个非负的整数。其中，它们各自的位数是按照**逆序**的方式存储的，并且它们的每个节点只能存储**一位**数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：
```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

## 方法


# 24. 两两交换链表中的节点
[Swap Nodes in Pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

## 题目描述
给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例:
```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```

## 方法
a, b, pre 三个变量

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        pre, pre.next = self, head
        while pre.next and pre.next.next: #考虑奇数和偶数
            a = pre.next 
            b = a.next # a,b是要调换的相邻元素
            pre.next, b.next, a.next =b, a, b.next # next连线重置
            pre = a
        return self.next
```


