---
title: python解leetcode拓扑排序
date: 2019-08-05
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   拓扑排序
        -   图
---

在图论中，**拓扑排序（Topological Sorting）**是一个有向无环图（DAG, Directed Acyclic Graph）的所有顶点的线性序列。且该序列必须满足下面两个条件：

1.  每个顶点出现且只出现一次。
2.  若存在一条从顶点 A 到顶点 B 的路径，那么在序列中顶点 A 出现在顶点 B 的前面。

有向无环图（DAG）才有拓扑排序，非DAG图没有拓扑排序一说。

**学习目标：**

-   207. 课程表s
-   210. 课程表 II

拓扑排序返回的结果是有向图中所有点依据有向边指向顺序的排列而成的点。

那么该如何获得一个有向图的拓扑排序的结果？

<!-- more -->

最直观的方法步骤如下

1.  找到图中入度（indegree）为0的点，然后记录这个点并删除这个点的所有出度（outdegree），也就是连接了这个点的其他点的入度
2.  检查图中是否有入度为0的点，如果有重复步骤（1）

重复步骤（1）-（2）直至无法找到入度为0的点，此时如果记录的点的数目与所有点的数目相同，那么说明图中不存在闭合的环，反之则存在。

**应用：**拓扑排序通常用来“排序”具有依赖关系的任务。

# 207. 课程表
[Course Schedule](https://leetcode-cn.com/problems/course-schedule)

现在你总共有 n 门课需要选，记为 0 到 n-1。

在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们: [0,1]

给定课程总量以及它们的先决条件，判断是否可能完成所有课程的学习？

示例 1:
```
输入: 2, [[1,0]] 
输出: true
解释: 总共有 2 门课程。学习课程 1 之前，你需要完成课程 0。所以这是可能的。
```
示例 2:
```
输入: 2, [[1,0],[0,1]]
输出: false
解释: 总共有 2 门课程。学习课程 1 之前，你需要先完成​课程 0；并且学习课程 0 之前，你还应先完成课程 1。这是不可能的。
```

**说明:**

-   输入的先决条件是由边缘列表表示的图形，而不是邻接矩阵。详情请参见图的表示法。
-   你可以假定输入的先决条件中没有重复的边。

**提示:**

1.  这个问题相当于查找一个循环是否存在于有向图中。如果存在循环，则不存在拓扑排序，因此不可能选取所有课程进行学习。
2.  通过 DFS 进行拓扑排序 - 一个关于Coursera的精彩视频教程（21分钟），介绍拓扑排序的基本概念。
3.  拓扑排序也可以通过 BFS 完成。

## 方法:队列

1.  遍历有向图中所有的边可以构造出上面提到的两个数据结构，时间复杂度为O(e)
2.  从In-Degree array中找所有点中入度数为0的点，构成初始队列，时间复杂度O(n)
3.  通过队首出列，调整与队首元素出度相连的所有点的入度数，并检查这些点的入度数是否为0，若是则入队列
4.  重复步骤(3)直至队列为空，此时若从队列中出列的所有点的数目为原来有向图中所有点的数目，则图中不存在闭合的环，且出列的顺序是一种可行的遍历方法。步骤(3)(4)的时间复杂度为O(n+e)

```python
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        indegree = [0 for i in range(numCourses)] # 每个元素的入度数 (先决条件数)
        connection = {i:[] for i in range(numCourses)} # 存放每个元素的出度连接的所有元素
        for link in prerequisites:
            connection[link[1]].append(link[0]) 
            indegree[link[0]] += 1
        
        zero_indegree = [] # （先觉条件为0）入度数为0的元素要入队
        for i in range(numCourses):
            if indegree[i]==0:
                zero_indegree.append(i)
        
        i=0 #遍历队列中的元素，出队的元素的相连接所有元素元素都可以-1减少一个先决条件
        while i<len(zero_indegree):
            for node in connection[zero_indegree[i]]:
                indegree[node] -= 1
                if indegree[node] == 0:
                    zero_indegree.append(node)# 入队
            i += 1
        if len(zero_indegree) == numCourses:
            return True
        else:
            return False
```

# 210. 课程表 II
[Course Schedule II](https://leetcode-cn.com/problems/course-schedule-ii)

现在你总共有 n 门课需要选，记为 0 到 n-1。

在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们: [0,1]

给定课程总量以及它们的先决条件，返回你为了学完所有课程所安排的学习顺序。

可能会有多个正确的顺序，你只要返回一种就可以了。如果不可能完成所有课程，返回一个空数组。

示例 1:
```
输入: 2, [[1,0]] 
输出: [0,1]
解释: 总共有 2 门课程。要学习课程 1，你需要先完成课程 0。因此，正确的课程顺序为 [0,1] 。
```
示例 2:
```
输入: 4, [[1,0],[2,0],[3,1],[3,2]]
输出: [0,1,2,3] or [0,2,1,3]
解释: 总共有 4 门课程。要学习课程 3，你应该先完成课程 1 和课程 2。并且课程 1 和课程 2 都应该排在课程 0 之后。
     因此，一个正确的课程顺序是 [0,1,2,3] 。另一个正确的排序是 [0,2,1,3] 。
```

## 方法
同207题的解法一样，只需要修改`return`即可

```python
class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        indegree = [0 for i in range(numCourses)] # 每个元素的入度数 (先决条件数)
        connection = {i:[] for i in range(numCourses)} # 存放每个元素的出度连接的所有元素
        for link in prerequisites:
            connection[link[1]].append(link[0]) 
            indegree[link[0]] += 1
        
        zero_indegree = [] # （先觉条件为0）入度数为0的元素要入队
        for i in range(numCourses):
            if indegree[i]==0:
                zero_indegree.append(i)
        
        i=0 #遍历队列中的元素，出队的元素的相连接所有元素元素都可以-1减少一个先决条件
        while i<len(zero_indegree):
            for node in connection[zero_indegree[i]]:
                indegree[node] -= 1
                if indegree[node] == 0:
                    zero_indegree.append(node)# 入队
            i += 1
        if len(zero_indegree) == numCourses:
            return zero_indegree
        else:
            return [] 
```

# 参考

-   [LeetCode 解题报告(207,210)--拓扑排序(Topological Sort)](https://wulc.me/2016/07/27/LeetCode%20%E8%A7%A3%E9%A2%98%E6%8A%A5%E5%91%8A(207,210)--%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F/)
