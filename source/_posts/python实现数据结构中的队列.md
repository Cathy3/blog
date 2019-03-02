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

<!-- more -->

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
队头变量`_head`记录当前队列的第一个元素位置
```python
class QueueUnderflow(ValueError):# 空队列无法 dequeue
    pass

class SQueue:
    def __init__(self, init_len=8):
        self._elems = [0]*init_len  # 队列元素
        self._head = 0 # 队列首元素位置的下标
        self._num = 0  # 表中元素个数
        self._len = init_len   # 当前表的长度，表满了的话，要换一个存储表
    def peek(self):
        if self._num == 0: # 队空状态
            raise QueueUnderflow
        return self._elems[self._head]
    def dequeue(self):
        if self._num == 0:  # 队空状态
            raise QueueUnderflow
        e = self._elems[self._head]
        self._head = (self._head+1) % self._len 
        self._num -= 1
        return e
    def enqueue(self, e):
        if self._num == self._len: # 队满状态
            self.__extend() # 扩大存储区
        # 新元素的入队位置
        self._elems[(self._head + self._num) % self._len]=e
        self._num += 1
    def __extend(self):  # 扩容
        old_len = self._len
        self._len *= 2
        new_elems = [0] * self._len
        for i in range(old_len):
            new_elems[i] = self._elems[(self._head + i)%old_len]
        self._elems, self._head = new_elems, 0
        
if __name__ == '__main__':
    l = SQueue()
    for i in range(10):
        l.enqueue(i+1)
    print(l.peek())
    l.dequeue()
    print(l.peek())
```
# LeetCode习题

## 设计一个双端队列
[Design Circular Deque](https://leetcode-cn.com/problems/design-circular-deque/)

## 滑动窗口最大值
[Sliding Window Maximum](https://leetcode-cn.com/problems/sliding-window-maximum/)