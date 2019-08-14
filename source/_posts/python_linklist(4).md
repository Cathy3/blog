---
title: python解LeetCode链表(4)
date: 2019-06-10
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 链表
---

-   2. 两数相加
-   445. 两数相加 II 
-   138. [复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

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

## 方法：偷懒做法
考虑：数字中是否有前置0？（除0以外，没有前置0） 有负数？

先求和，再构建链表。这个方法比较暴力。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        num1 = ''
        num2 = ''
        while l1:#243
            num1 += str(l1.val)
            l1 = l1.next
        while l2:#564
            num2 += str(l2.val)
            l2 = l2.next
        add = str(int(num1[::-1]) + int(num2[::-1]))[::-1] #342+465=807---708
        head = ListNode(add[0])
        res = head
        for i in range(1,len(add)):
            node = ListNode(add[i])
            head.next = node
            head = head.next
        return res
```

445. 两数相加 II
给定两个非空链表来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储单个数字。将这两数相加会返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

进阶: 如果输入链表不能修改该如何处理？换句话说，你不能对列表中的节点进行翻转。

示例:
```
输入: (7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
输出: 7 -> 8 -> 0 -> 7
```

## 方法
先求出和，再构建链表
```python
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        num1 = ''
        num2 = ''
        while l1:
            num1 += str(l1.val)
            l1 = l1.next
        while l2:
            num2 += str(l2.val)
            l2 = l2.next
        add = str(int(num1) + int(num2))
        head = ListNode(add[0])
        answer = head
        for i in range(1, len(add)):
            node = ListNode(add[i])
            head.next = node
            head = head.next
        return answer
```

# 138. 复制带随机指针的链表
给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。

要求返回这个链表的深拷贝。 

示例：
```
输入：
{"$id":"1","next":{"$id":"2","next":null,"random":{"$ref":"2"},"val":2},"random":{"$ref":"2"},"val":1}

解释：
节点 1 的值是 1，它的下一个指针和随机指针都指向节点 2 。
节点 2 的值是 2，它的下一个指针指向 null，随机指针指向它自己。
 
```
提示：你必须返回给定头的拷贝作为对克隆列表的引用。

## 方法
使用hashtable，在这个hash表里，记录了老链表和新链表的每一组对应。这样先构造了一个纯next的链表，然后再次循环就能得到带random的链表了。

```python
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        nodeDict = dict()
        dummy = Node(0, None, None)
        nodeDict[head] = dummy
        newHead, pointer = dummy, head
        
        while pointer:
            nodeDict[pointer] = Node(pointer.val, pointer.next, None)
            newHead.next = nodeDict[pointer]
            newHead, pointer = newHead.next, pointer.next
        
        pointer = head
        while pointer:
            if pointer.random:
                nodeDict[pointer].random = nodeDict[pointer.random]
            pointer = pointer.next
        return dummy.next
```