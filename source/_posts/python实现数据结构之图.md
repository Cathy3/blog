---
title: python实现数据结构之图
date: 2019-03-10
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   图
mathjax: true
---

**学习目标：**

-    实现有向图、无向图、有权图、无权图的邻接矩阵和邻接表表示方法
-    实现图的深度优先搜索、广度优先搜索
实现 Dijkstra 算法、A* 算法
实现拓扑排序的 Kahn 算法、DFS 算法
对应的 LeetCode 练习题
200 Number of Islands（岛屿的个数）
英文版：https://leetcode.com/problems/number-of-islands/description/
中文版：https://leetcode-cn.com/problems/number-of-islands/description/
Valid Sudoku（有效的数独）
英文版：https://leetcode.com/problems/valid-sudoku/
中文版：https://leetcode-cn.com/problems/valid-sudoku/

<!-- more -->

# 实现一个邻接表表示的有向带权图
每个顶点的所有邻接点和对应的边的权重用字典表示。

对于无权图，可以只将顶点的所有邻接点放入 list 中即可。

图的所有顶点名称及对应的顶点对象用字典表示。

```python
class Vertex:#顶点
    def __init__(self,key):
        self.id = key
        self.connectedTo = {} #邻接点，及相应的权重
        
    def addNeighbor(self, nbr, weight=0):
        self.connectedTo[nbr] = weight
    def __str__(self):
        return str(self.id) + ' connectedTo: '+str([x.id for x in self.connectedTo])
    def getConnections(self): #多有邻接点
        return self.connectedTo.keys()
    def getId(self):
        return self.id
    def getWeight(self,nbr):
        return self.connectedTo[nbr]
    
class Graph: # 邻接表表示的有向带权图
    def __init__(self):
        self.vertList = {} # 所有顶点
        self.numVertices = 0
        
    def addVertex(self, key): #添加顶点
        self.numVertices += 1
        newVertex = Vertex(key)
        self.vertList[key] = newVertex
        return newVertex
    def getVertex(self, n):
        if n in self.vertList:
            return self.vertList[n]
        else:
            return None
    def __contains__(self,n):
        return n in self.vertList
    def addEdge(self,f,t,cost=0): #添加边
        if f not in self.vertList:
            self.addVertex(f)
        if t not in self.vertList:
            self.addVertex(t)
        self.vertList[f].addNeighbor(self.vertList[t], cost)
        
    def getVertices(self):
        return self.vertList.keys()
    def __iter__(self):
        return iter(self.vertList.values())
    
g = Graph()
for i in range(6):
    g.addVertex(i)
print(g.getVertex(3))
print(g.vertList)
print(3 in g)
g.addEdge(0,1,5)
g.addEdge(0,5,2)
g.addEdge(1,2,4)
g.addEdge(2,3,9)
g.addEdge(3,4,7)
g.addEdge(3,5,3)
g.addEdge(4,0,1)
g.addEdge(5,4,8)
g.addEdge(5,2,1)
print(g.getVertices())
for v in g:
    for w in v.getConnections():
        print('(%s,%s)'%(v.getId(), w.getId()))
```

输出：

```
3 connectedTo: []
{0: <__main__.Vertex object at 0x000001AEC9423B70>, 1: <__main__.Vertex object at 0x000001AEC903F748>, 2: <__main__.Vertex object at 0x000001AEC85F8CC0>, 3: <__main__.Vertex object at 0x000001AEC9415FD0>, 4: <__main__.Vertex object at 0x000001AEC9415EF0>, 5: <__main__.Vertex object at 0x000001AEC9415B70>}
True
dict_keys([0, 1, 2, 3, 4, 5])
(0,1)
(0,5)
(1,2)
(2,3)
(3,4)
(3,5)
(4,0)
(5,4)
(5,2)
```

# 图的遍历
遍历图最常用的有两种方式，就是你常听到的 BFS 和 DFS.

-   BFS: Breadth First Search，广度优先搜索
-   DFS: Depdth First Search，深度优先搜索

## BFS
BFS 类似于树的层序遍历，从第一个节点开始，先访问离 A 最近的点，接着访问次近的点。

```python
from collections import deque

GRAPH = {  # 邻接表表示的无向无权图
    'A': ['B', 'F'],
    'B': ['C', 'I', 'G'],
    'C': ['B', 'I', 'D'],
    'D': ['C', 'I', 'G', 'H', 'E'],
    'E': ['D', 'H', 'F'],
    'F': ['A', 'G', 'E'],
    'G': ['B', 'F', 'H', 'D'],
    'H': ['G', 'D', 'E'],
    'I': ['B', 'C', 'D'],
}


class Queue(object): # 队列辅助
    def __init__(self):
        self._deque = deque()

    def push(self, value):
        return self._deque.append(value)

    def pop(self):
        return self._deque.popleft()

    def __len__(self):
        return len(self._deque)


def bfs(graph, start):
    search_queue = Queue()
    search_queue.push(start)# 起点先入队
    searched = set() # 已经访问过的结点
    while search_queue:   # 队列不为空就继续
        cur_node = search_queue.pop() # 出队
        if cur_node not in searched: # 若是未访问的结点
            yield cur_node # 读出
            searched.add(cur_node) # 添加到记录中
            for node in graph[cur_node]: # 所有邻接点
                search_queue.push(node)# 起点的所有邻接点入队

print([i for i in bfs(GRAPH, 'A')])
```
输出

```
['A', 'B', 'F', 'C', 'I', 'G', 'E', 'D', 'H']
```

## DFS
深度优先搜索(DFS)是每遇到一个节点，如果没有被访问过，就直接去访问它的邻居节点，不断加深。

```python
GRAPH = {  #邻接表表示的无向无权图
    'A': ['B', 'F'],
    'B': ['C', 'I', 'G'],
    'C': ['B', 'I', 'D'],
    'D': ['C', 'I', 'G', 'H', 'E'],
    'E': ['D', 'H', 'F'],
    'F': ['A', 'G', 'E'],
    'G': ['B', 'F', 'H', 'D'],
    'H': ['G', 'D', 'E'],
    'I': ['B', 'C', 'D'],
}

DFS_SEARCHED = set()


def dfs(graph, start):
    if start not in DFS_SEARCHED: # 若是未访问的结点
        print(start)
        DFS_SEARCHED.add(start)
    for node in graph[start]: # 所有邻接点
        if node not in DFS_SEARCHED:# 一旦是未访问的结点
            dfs(graph, node)  # 递归


dfs(GRAPH, 'A')  # A B C I D G F E H
```

# 参考
-    《Problem Solving with Algorithms and Data Structures using Python》
-    [18_图与图的遍历](https://python-data-structures-and-algorithms.readthedocs.io/zh/latest/18_%E5%9B%BE%E4%B8%8E%E5%9B%BE%E7%9A%84%E9%81%8D%E5%8E%86/graph/)
-    [数据结构之图](https://www.zybuluo.com/guoxs/note/249812)
