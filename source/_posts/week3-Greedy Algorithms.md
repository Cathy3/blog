---
title: week3-贪心算法
date: 2018-10-24
categories: 
		- Algorithm
tags:  
		- python
		- Algorithm
        - 背包问题
mathjax: true
---
# 概述
贪心算法是指，在对问题求解时，每一步选择中总是做出在当前看来是最好的选择。也就是说，不从整体最优上加以考虑，他所做出的是在某种意义上的局部最优解。

## 实现过程
从问题的某一初始解出发；while 能朝给定总目标前进一步 do，求出可行解的一个解元素；
最后，由所有解元素组合成问题的一个可行解。

1. 创建数学模型来描述问题。
2. 把求解的问题分成若干个子问题。
3. 对每一子问题求解，得到子问题的局部最优解。
4. 把子问题的解局部最优解合成原来解问题的一个解。

注意：
- 贪心算法与动态规划的不同在于它对每个子问题的解决方案都做出选择，不能回退。动态规划则会保存以前的运算结果，并根据以前的结果对当前进行选择，有回退功能。
- 贪心法可以解决一些最优化问题，如：求图中的最小生成树、求哈夫曼编码……
- 对于大部分的问题，贪心法通常都不能找出最佳解（不过也有例外），因为他们一般没有测试所有可能的解。贪心法容易过早做决定，因而没法达到最佳解。例如，所有对图着色问题。

<!-- more -->

## 学习目标
 - Practice implementing greedy solutions
 - Build greedy algorithms
 - Create a program for changing money optimally
 - Create a program for maximizing the value of a loot
 - Create a program for maximizing the number of prize places in a competition

1. Apply greedy strategy to solve various computational problems. This will usually require you to design
an algorithm that repeatedly makes the most proftable move to construct a solution. You will then
need to show that the moves of your algorithm are safe, meaning that they are consistent with at least
one optimal solution.
2. Design and implement an efcient greedy algorithm for the following problems:
(a) changing money with a minimum number of coins;
(b) maximizing the total value of a loot;
(c) maximizing revenue in online ad placement;
(d) minimizing work while collecting signatures;
(e) maximizing the number of prize places in a competition;
(f) fnally, maximizing your salary!

# 兑换零钱(Money Change)
**Task.** 根据输入值，找到需要兑换 1, 5, 10面额硬币的最少个数.

**Input Format.** 输入一个整数$m$.

**Constraints.** $1 ≤ n ≤ 10^3 $.

**Output Format.** Output the minimum number of coins with denominations 1, 5, 10 that changes $m$.

**Sample 1.**
```
Input:
28
Output:
6
```
28 = 10 + 10 + 5 + 1 + 1 + 1.

## 方法一


```python
def get_change(m):
    coins = [10, 5, 1]
    n = 0
    for a in coins:        
        while m >= a:
            n += 1
            m -= a
        if m == 0:
            break
    return n
print(get_change(2))
print(get_change(28))
print(get_change(999))
```

    2
    6
    104


## 方法二


```python
def get_changeC(m):
    coins = [10, 5, 1]
    n = 0
    for a in coins:
        # Update the the number of coins 'held' in the amount.
        n += m // a
        # Put remainder to the residuary amount.
        m %= a
    return n
print(get_changeC(2))
print(get_changeC(28))
print(get_changeC(999))
```

    2
    6
    104


# 背包问题(Maximum Value of the Loot )
**Task.** A thief finds much more loot than his bag can fit. Help him to find the most valuable combination of items assuming that any fraction of a loot item can be put into his bag.

**Input Format.** 输入包可承受的重量(capacity)$W$，$n$件掠夺品的重量weights和价值values分别是$w_i,v_i$

**Constraints.** $1 ≤ n ≤ 10^3, 0 ≤W≤ 2·10^6, 0 ≤v_i≤ 2·10^6, 0 ≤w_i≤ 2·10^6 $.所有数字都是整数.

**Output Format.** Output the maximal value of fractions of items that ft into the knapsack.

**Sample 1.**
```
Input:
50, [20,50,30], [60,100,120]
Output:
180.0000
```
选了第1件和第3件东西。
## 方法一


```python
def get_optimal_value(capacity, weights, values):
    value = 0
    n = len(weights)
    # A = [None] * n
    pers = [None] * n
    for i in range(n):
        pers[i] = values[i] / weights[i]
    
    for _ in range(n):
        if capacity == 0:
            return value
        m = pers.index(max(pers))
        a = min(weights[m], capacity)
        value += a*pers[m]
        
        weights[m] -= a
        if weights[m] == 0:
            pers[m] = 0
        # A[m] += a
        capacity -= a
        
    return value

print("{:.10f}".format(get_optimal_value(10, [30], [500])))
print("{:.10f}".format(get_optimal_value(50, [20,50,30], [60,100,120])))
```

    166.6666666667
    180.0000000000


## 方法二Optimization
首先按降序将 $\frac{v}{w}$ 排序


```python
def get_optimal_value(capacity, weights, values):
    value = 0
    weight_value_pairs = sorted(list(zip(weights, values)), 
                                key = lambda x:x[1]/x[0],
                                reverse=True)
   
    for (w, v) in weight_value_pairs:
        # If the item fit into the knapsack, put it and recalculate space left.
        if capacity - w >= 0:
            value += v
            capacity -= w
        else:
            # Otherwise calculate weight of unit of the item and fill
            # the knapsack's left space.
            value += (v / w) * capacity
            capacity = 0
        if not capacity:
            break
            
    return value

print("{:.10f}".format(get_optimal_value(10, [30], [500])))
print("{:.10f}".format(get_optimal_value(50, [20,50,30], [60,100,120])))
```

    166.6666666667
    180.0000000000


# 广告点击最大收益
（Maximum Advertisement Revenue）

**Task.**  You have $n$ ads to place on a popular Internet page. For each ad, you know how much is the advertiser willing to pay for one click on this ad. You have set up $n$
slots on your page and estimated the expected number of clicks per day for each
slot. Now, your goal is to distribute the ads among the slots to maximize the
total revenue.

**Input Format.** 给定两组序列$a_1,a_2,...,a_n$($a_i$ 是第 $i$ 条广告的点击利润)，$b_1,b_2,...,b_n$($b_i$ 是第 $i$ 个位置的平均每天点击数)，把它们对应分成$n$组$(a_i,b_j)$使他们乘积的总和最大。

**Constraints.** $1 ≤ n ≤ 10^3, -10^5 ≤a_i,b_i≤ 10^5


**Output Format.** 输出 $\sum^n_{i=1}a_ic_i$ 的最大值。$c_1,c_2,...,c_n$是$b_1,b_2,...,b_n$的重排列

**Sample 1.**
```
Input:
[1,3,-5], [-2,4,1]
Output:
23
```
$23 = 3 · 4 + 1 · 1 + (−5) · (−2).$

先排好序，最大的在前，最大的两个乘积，随后循环,这样得到的乘积和最大。


```python
def max_dot_product(a, b):
    a = sorted(a, reverse=True)
    b = sorted(b, reverse=True)
    res = 0
    for i in range(len(a)):
        res += a[i] * b[i]
    return res


print(max_dot_product([23], [39]))
print(max_dot_product([1,3,-5], [-2,4,1]))
```

    897
    23


# 找房客签字的最佳时间点
Collecting Signatures

**Task.**  You are responsible for collecting signatures from all tenants of a certain building. For each tenant, you know a period of time when he or she is at home.
You would like to collect all signatures by visiting the building as few times as
possible.Your goal is to mark as few points on a line as possible so that each segment contains at least one marked point.找最少的标记时间点数$m$，使每个时间段内都有标记时间点。

**Input Format.** $n$段时间 $\{[a_0,b_0],[a_1,b_1],...,[a_{n-1},b_{n-1}]\}$

**Constraints.** $1 ≤ n ≤ 100, -0≤a_i ≤b_i≤ 10^9

**Output Format.** 标记的位置点

**Sample 1.**
```
Input:
[(4,7), (1,3), (2,5), (5,6)]
Output:
3 6
```



```python
def optimal_points(segments):
    # 按结束时间先后排序
    segments.sort(key = lambda x:x[1]) 
    points = []
    for s in segments:
        points.append(s[0])
        points.append(s[1])    
    
    result = []
    n = len(segments)       
    c = 1
    i = 1
    result.append(points[c])   # 加入第一个结束时间点为标记点
    while i < n:
        if points[i*2] > points[c]: # 如果下一段起始时间点晚于标记点
            c = i*2+1               # 则把这段的结束时间点加入标记点
            result.append(points[c])            
        i += 1
    return result

points1 = optimal_points([(1,3), (2,5), (3,6)])
#print(len(points1))
for p in points1:
    print(p, end=' ')
print("\n")    
points2 = optimal_points([(4,7), (1,3), (2,5), (5,6)])
#print(len(points2))
for p in points2:
    print(p, end=' ')
```

    3 
    
    3 6 

# 分不同数量的奖品

**Task.**  你在给孩子们组织一场有趣的竞赛。你有$n$ 个糖果作为奖品.你想把这些奖品给比赛前 $k$ 名的孩子。名次越高，获奖糖果应该越多。为了使尽可能多孩子开心，你要找最多的获奖名额$k$.


**Input Format.** 整数 $n$

**Constraints.** $1 ≤ n ≤ 10^9 $

**Output Format.** 获奖名额 $k$ 和 分别获得的糖果数 (每个名次的获奖糖果数不应相同.)

**Sample 1.**
```
Input:
7
Output:
3
1 2 4 
```


```python
def optimal_summands(n):
    summands = [1]   #被加数
    n -= 1
    while n:
        last_element = summands[-1] 
        # Save move: check whether the incremented last element can be used as
        # the next summand.
        if (last_element + 1) * 2 <= n: # 如果最后一个数比n的一半还小
            n -= last_element + 1
            summands.append(last_element + 1)
        else:
            if last_element >= n:   # 如果最后一个数大于n
                n += summands.pop()
            summands.append(n)
            n = 0    
    return summands

summands = optimal_summands(2)
print(len(summands))
for x in summands:
    print(x, end=' ')
print("\n")
summands = optimal_summands(6)
print(len(summands))
for x in summands:
    print(x, end=' ')
print("\n")
summands = optimal_summands(7)
print(len(summands))
for x in summands:
    print(x, end=' ')
print("\n")
```

    1
    2 
    
    3
    1 2 3 
    
    3
    1 2 4 
    


这里设置<=n 而不是<n 是要考虑要奖品数为2的情况，必须把之前的1退回去。

# 最大薪水
Maximum Salary

**Task.**  As the last question of a successful interview, your boss gives you a few pieces of paper with numbers on it and asks you to compose a largest number from these numbers. The resulting number is going to be your salary, so you are very much interested in maximizing this number. How can you do this?不同的数拼成一个最大数(这些数未必只是一位数。比如23和3，应该拼成323最大)

**Input Format.** $n$个数 $a_0,a_1,...,a_n$

**Constraints.** $1 ≤ n ≤ 100, 1≤a_i ≤ 10^3$

**Output Format.** 可组成的最大数

**Sample 1.**
```
Input:
[21,2]
Output:
221
```


```python
import sys

def isGreaterOrEqual(digit,maxDigit):
    d = int(str(digit)+str(maxDigit))
    m = int(str(maxDigit)+str(digit))            
    return (d >= m)



def largest_number(a):
    res = ""
    r=[]
    while a != []:
        maxDigit = 0
        for digit in a:
            if isGreaterOrEqual(digit,maxDigit):
                maxDigit = digit
        r.append(maxDigit)
        a.remove(maxDigit)              
    for rr in r:
        res += str(rr)
    return res
print(largest_number([21,2]))
print(largest_number([23,39,92]))
print(largest_number([9,4,6,1,9]))
```

    221
    923923
    99641

参考：
 - [Coursera：algorithmic toolbox(week3)](https://www.coursera.org/learn/algorithmic-toolbox/home/week/3)
