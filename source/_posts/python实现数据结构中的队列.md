---
title: python实现数据结构中的队列
date: 2019-03-01
categories: 
	- Algorithm
tags:  
        - python
        - LeetCode
        - 数据结构
        - 队列
---
# 用数组实现一个顺序队列
list表头入队，表尾出队。反过来也可以。

实现的基本操作有：
- enqueue 入队，要把表中其余元素全部后移，再插入首元素，需要 $O(n)$时间
- dequeue 出队,$O(1)$

```python
class Queue:
    def __init__(self):
        self.items = []
    def enqueue(self, item): # 入队
        self.items.insert(0,item)
    def dequeue(self): # 出队
        return self.items.pop()
    
if __name__ == '__main__':
    q = Queue()
    q.enqueue(4)
    q.enqueue(5)
    q.enqueue(6)
    print(q.dequeue())
```
输出：
```
4
```

# 用链表实现一个链式队列
等同于用带表尾指针的单链表。链尾入队，链首出队。

实现的基本操作有：
- enqueue 入队   $O(1)$
- dequeue 出队   $O(1)$
- peek 查看队首元素 $O(1)$

```python
class Node: # 链表的结点
    def __init__(self, x, next_=None):
        self.val = x
        self.next = next_

class LQueue: # 
    def __init__(self): # 空表
        self._head = None
        self._rear = None # 尾结点
    
    def enqueue(self, x): # 入队
        if self._head is None:
            self._head = Node(x, self._head)
            self._rear = self._head
        else:
            self._rear.next = Node(x)
            self._rear = self._rear.next
    def dequeue(self): # 出队
        if self._head is None:
            return 
        x = self._head.val
        self._head = self._head.next
        return x
    def peek(self): # 查看最早入队的元素
        if self._head is None:
            return
        return self._head.val 
    
if __name__ == '__main__':
    l = LQueue()
    l.enqueue(4)
    l.enqueue(5)
    print(l.peek())
    l.enqueue(6)
    l.dequeue()
    print(l.peek())
```
输出：
```
4
5
```

# 实现一个循环队列

# LeetCode习题
Design Circular Deque（设计一个双端队列）
英文版：https://leetcode.com/problems/design-circular-deque/
中文版：https://leetcode-cn.com/problems/design-circular-deque/
Sliding Window Maximum（滑动窗口最大值）
英文版：https://leetcode.com/problems/sliding-window-maximum/
中文版：https://leetcode-cn.com/problems/sliding-window-maximum/