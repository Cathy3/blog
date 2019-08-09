---
title: python解leetcode图的最短路径问题
date: 2019-08-07
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   图
---

**学习目标：**

-   Dijkstra算法
-   Floyd-Warshall算法
-   Bellman-Ford算法
-   spfa算法
-   leetcode743. 网络延迟时间
-   leetcode787. K 站中转内最便宜的航班

<!-- more -->

# Dijkstra(迪杰斯特拉)算法
典型的单源最短路径算法，用于计算一个节点到其他所有节点的最短路径。主要特点是以起始点为中心向外层层扩展，直到扩展到终点为止。注意该算法要求图中不存在负权边。时间复杂度O(n^2)

具体步骤：

1.  找出路径最近的节点S，即可在最短时间内前往的节点
2.  对于该节点的所有邻居，检查是否有经过S前往它们的更短路径，若有，就更新其路径距离。
3.  重复前两步，直到对每个节点都这样做了。
4.  计算最终距离。

# Floyd-Warshall（弗洛伊德算法）
又称为插点法，是一种利用动态规划的思想寻找给定的加权图中多源点之间最短路径的算法，与Dijkstra算法类似。

时间复杂度O(n^3)

# Bellman-Ford算法
是求含负权图的单源最短路径的一种算法，效率较低，代码难度较小。其原理为连续进行松弛，在每次松弛时把每条边都更新一下，若在n-1次松弛后还能更新，则说明图中有负环，因此无法得出结果，否则就完成。

1.  初始化：将除源点外的所有顶点的最短距离估计值 d[v] ——>+∞, d[s]——>0;
2.  迭代求解：反复对边集E中的每条边进行松弛操作，使得顶点集V中的每个顶点v的最短距离估计值逐步逼近其最短距离；（运行|v|-1次）
3.  检验负权回路：判断边集E中的每一条边的两个端点是否收敛。如果存在未收敛的顶点，则算法返回false，表明问题无解；否则算法返回true，并且从源点可达的顶点v的最短距离保存在 d[v]中。

# 743. 网络延迟时间
有 N 个网络节点，标记为 1 到 N。

给定一个列表 times，表示信号经过有向边的传递时间。 times[i] = (u, v, w)，其中 u 是源节点，v 是目标节点， w 是一个信号从源节点传递到目标节点的时间。

现在，我们向当前的节点 K 发送了一个信号。需要多久才能使所有节点都收到信号？如果不能使所有节点收到信号，返回 -1。

注意:

1.  N 的范围在 [1, 100] 之间。
2.  K 的范围在 [1, N] 之间。
3.  times 的长度在 [1, 6000] 之间。
4.  所有的边 times[i] = (u, v, w) 都有 1 <= u, v <= N 且 0 <= w <= 100。

## 题目大意
单源有向加权图中，求源节点K到所有其它目标节点的最短路径，取最大值。如果有节点不可抵达，返回-1.

## 方法1：Dijkstra算法
时间复杂度是 O(N ^ 2 + E)，空间复杂度是O(N+E).

```python
class Solution:
    def networkDelayTime(self, times: List[List[int]], N: int, K: int) -> int:
        K -= 1 # 起点
        nodes = collections.defaultdict(list) # 存储节点的邻接点和相连边的权重。
        for u,v,w in times:
            nodes[u-1].append((v-1, w))
        
        dist = [float('inf')] * N # 起点到所有节点距离。
        dist[K] = 0
        done = set() # 记录处理过的节点
        
        for _ in range(N):
            # 获取离起点最近的节点
            smallest = min((d,i) for (i,d) in enumerate(dist) if i not in done)[1]
            for v,w in nodes[smallest]:#找最近点的所有邻接点。
                if v not in done and dist[smallest]+w < dist[v]: # 更新邻接点的距离
                    dist[v] = dist[smallest]+w 
            done.add(smallest) # 该节点标记为处理过
        return -1 if float('inf') in dist else max(dist)
```

## 方法2:Floyd-Warshall算法。
这个算法超时 Time Limit Exceed(TLE).

时间复杂度O(n^3)， 空间复杂度O(n^2)。

```python
class Solution:
    def networkDelayTime(self, times: List[List[int]], N: int, K: int) -> int:
        d = [[float('inf')] * N for _ in range(N)] # 二维矩阵
        for time in times:
            u, v, w = time[0] - 1, time[1] - 1, time[2]
            d[u][v] = w
        for i in range(N):
            d[i][i] = 0
        for k in range(N): # 遍历更新距离
            for i in range(N):
                for j in range(N):
                    d[i][j] = min(d[i][j], d[i][k] + d[k][j])
        return -1 if float('inf') in d[K - 1] else max(d[K - 1])
```

## 方法3：Bellman-Ford算法
这个算法超时TLE。

时间复杂度O(ne)， 空间复杂度O(n)。
```python
class Solution:
    def networkDelayTime(self, times: List[List[int]], N: int, K: int) -> int:
        dist = [float('inf')] * N
        dist[K - 1] = 0
        for _ in range(N):
            for time in times:
                u = time[0] - 1
                v = time[1] - 1
                w = time[2]
                dist[v] = min(dist[v], dist[u] + w)
        return -1 if float('inf') in dist else max(dist)
```

# 787. K 站中转内最便宜的航班

有 n 个城市通过 m 个航班连接。每个航班都从城市 u 开始，以价格 w 抵达 v。

现在给定所有的城市和航班，以及出发城市 src 和目的地 dst，你的任务是找到从 src 到 dst 最多经过 k 站中转的最便宜的价格。 如果没有这样的路线，则输出 -1。

示例 1:
```
输入: 
n = 3, edges = [[0,1,100],[1,2,100],[0,2,500]]
src = 0, dst = 2, k = 1
输出: 200
```

提示：

-   n 范围是 [1, 100]，城市标签从 0 到 n - 1.
-   航班数量范围是 [0, n * (n - 1) / 2].
-   每个航班的格式 (src, dst, price).
-   每个航班的价格范围是 [1, 10000].
-   k 范围是 [0, n - 1].
-   航班没有重复，且不存在环路

## 方法：队列+BFS
可以直接判断在指定的k步以内能不能走到dst，不会进行更多的搜索了，因此这个方法要快很多。

这个队列存放的是当我们进行第step次搜索时，搜索到的当前的节点，以及走到当前节点的花费。所以当当前节点走到dst时，更新最小花费。

时间复杂度是O(KN)，空间复杂度是O(N).

```python
class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, K: int) -> int:
        graph = collections.defaultdict(dict)
        for u, v, e in flights:
            graph[u][v] = e
        ans = float('inf')
        que = collections.deque()
        que.append((src, 0))
        step = 0
        while que:
            for i in range(len(que)):
                cur, cost = que.popleft()
                if cur == dst:
                    ans = min(ans, cost) #走到dst,更新最小花费
                for v, w in graph[cur].items(): # 所有邻居点
                    if cost + w > ans:
                        continue
                    que.append((v, cost + w)) # 走到当前节点的更小的花费
            if step > K: break
            step += 1
        return -1 if ans == float('inf') else ans
```