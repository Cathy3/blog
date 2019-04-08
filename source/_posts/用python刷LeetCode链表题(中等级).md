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

-   24. 两两交换链表中的节点

<!-- more -->

# LeetCode 24. 两两交换链表中的节点
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

