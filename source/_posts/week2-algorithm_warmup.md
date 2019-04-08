---
title: week2-Algorithm Warmup
date: 2018-09-28
categories: 
		- Algorithm
tags:  
		- python
		- Algorithm
        - Fibonacci
mathjax: true
---

 - Estimate the running time of an algorithm
 - Practice implementing efficient solutions
 - Practice solving programming challenges
 - Implement programs that are several orders of magnitude faster than straightforward programs

# Fibonacci Numbers
## 斐波那契数列
**Task.** Given an integer $n$,  find the last digit of the nth Fibonacci number $F_n$.

<!-- more -->

**Input Format.** The input consists of a single integer $n$.

**Constraints.** $0 ≤ n ≤ 45 $.

**Output Format.** Output $F_n$.

**Sample 1.**
```
Input:
10
Output:
55
```
$F_{10} = 55$

0 1 1 2 3 5 8 13 

## Naive Algorithm
递归 
```python
def calc_fib(n):
    if (n <= 1):
        return n

    return calc_fib(n - 1) + calc_fib(n - 2)

print(calc_fib(6))
print(calc_fib(20))
print(calc_fib(34))
```

    8
    6765
    5702887
时间复杂度：$O(2^n)$


## Fast Algorithm
动态规划
-   递归 + 记忆化 -> 递推
-   递推公式：A[i] = A[i-1] + A[i-2]

```python
def calc_fibFast(n):
    A = [None]* (n+1)
    A[0] = 0
    A[1] = 1
    for i in range(2, n+1):
        A[i] = A[i-1] + A[i-2]
    return A[n]

print(calc_fibFast(6))
print(calc_fibFast(20))
print(calc_fibFast(34))
```

    8
    6765
    5702887

时间复杂度：$O(n)$


```python
def calc_fibFast(n):
    if n <= 1:
        return n

    previous = 0
    current  = 1

    for _ in range(n - 1):
        previous, current = current, previous + current

    return current

print(calc_fibFast(6))
print(calc_fibFast(20))
print(calc_fibFast(34))
```

    8
    6765
    5702887


# Last Digit of a Large Fibonacci Number
## 求斐波那契数的个位数

**Task.** Given an integer $n$,  fnd the last digit of the nth Fibonacci number $F_n$ mod $m$ (that is, $F_n$ mod 10).

**Input Format.** The input consists of a single integer $n$.

**Constraints.** $0 ≤ n ≤ 10^{7} $.

**Output Format.** Output the last digit of $F_n$.

**Sample 1.**
```
Input:
3
Output:
2
```
$F_3 = 2$.
## Naive Algorithm



```python
def get_fibonacci_last_digit_naive(n):
    if n <= 1:
        return n

    previous = 0
    current  = 1

    for _ in range(n - 1):
        previous, current = current, previous + current

    return current % 10


print(get_fibonacci_last_digit_naive(6))
print(get_fibonacci_last_digit_naive(20))
print(get_fibonacci_last_digit_naive(34))
print(get_fibonacci_last_digit_naive(331))
```

    8
    5
    7
    9


## Fast Algorithm
每次只保留斐波那契数的个位数


```python
def get_fibonacci_last_digit_Fast(n):
    if n <= 1:
        return n

    previous = 0
    current  = 1

    for _ in range(n - 1):
        previous, current = current, (previous + current) % 10

    return current


print(get_fibonacci_last_digit_Fast(6))
print(get_fibonacci_last_digit_Fast(20))
print(get_fibonacci_last_digit_Fast(34))
print(get_fibonacci_last_digit_Fast(331))
```

    8
    5
    7
    9


# Greatest Common Divisor
## 最大公约数
**Task.** Given two integers $a$ and $b$, find their greatest common divisor.

**Input Format.** The two integers $a$ and $b$ are given in the same line separated by space..

**Constraints.** $1 ≤ a, b ≤ 2·10^{9}$.

**Output Format.** Output GCD(a, b).

**Sample 1.**
```
Input:
18 35
Output:
1
```
18 and 35 do not have common non-trivial divisors.
## Naive Algorithm
同时能整除a和b的最大的数


```python
def gcd_naive(a, b):
    current_gcd = 1
    for d in range(2, min(a, b) + 1):
        if a % d == 0 and b % d == 0:
            if d > current_gcd:
                current_gcd = d

    return current_gcd
print(gcd_naive(12, 4))
print(gcd_naive(18, 35))
print(gcd_naive(28851538, 1183019))
```

    4
    1
    17657


## Fast Algorithm
辗转相除法， 又名欧几里德算法（Euclidean algorithm）


```python
def gcd_Euclid(a, b):
    if b == 0:
        return a
    r = a % b
    return gcd_Euclid(b, r)
    
print(gcd_Euclid(12, 4))
print(gcd_Euclid(18, 35))
print(gcd_Euclid(28851538, 1183019))
```

    4
    1
    17657


# Least Common Multiple
## 最小公倍数
**Task.** Given two integers $a$ and $b$, fnd their least common multiple.

**Input Format.** The two integers $a$ and $b$ are given in the same line separated by space..

**Constraints.** $1 ≤ a, b ≤ 2·10^{9}$.

**Output Format.** Output the least common multiple of $a$ and $b$.

**Sample 1.**
```
Input:
6 8
Output:
24
```
Among all the positive integers that are divisible by both 6 and 8 (e.g., 48, 480, 24), 24 is the smallest
one.
## Naive Algorithm
同时是a和b的倍数的最小值


```python
def lcm_naive(a, b):
    for l in range(1, a*b + 1):
        if l % a == 0 and l % b == 0:
            return l

    return a*b

print(lcm_naive(6, 8))
print(lcm_naive(28851538, 1183019))
```

    24
    1933053046


## Fast Algorithm
性质：两个数的乘积等于这两个数的最大公约数与最小公倍数的积。用辗转相除法求出最大公约数，再求最小公倍数。


```python
def gcd_Euclid(a, b):
    if b == 0:
        return a
    r = a % b
    return gcd_Euclid(b, r)

def lcm_fast(a, b):
    return a*b // gcd_Euclid(a, b)

print(lcm_fast(6, 8))
print(lcm_fast(28851538, 1183019))
```

    24
    1933053046


# Fibonacci Number modulo m
## 斐波那契数取余

| $i$ | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
| ---- | --- |
| $F_i$ | 0 | 1 | 1 | 2 | 3 | 5 | 8 | 13 | 21 | 34 | 55 | 89 | 144 | 233 | 377 | 610 |
| $F_i$ mod 2 | 0 | 1 | 1 | 0 | 1 | 1 | 0 | 1 | 1 | 0 | 1 | 1 | 0 | 1 | 1 | 0 |
| $F_i$ mod 3 | 0 | 1 | 1 | 2 | 0 | 2 | 2 | 1|  0 | 1 | 1 | 2 | 0 | 2 | 2 | 1 |



可以看出序列是有周期性的。模3以01120221循环，模2以011循环。此类循环都是以01开始，称为皮萨诺周期(pisano period)

$F_(2015)$ mod 3 只需要找到2015被8除的余数. Since 2015 = 251 · 8 + 7, we
conclude that $F_(2015)$ mod 3 = $F_7$ mod 3 = 1.

**Task.** Given two integers $m$ and $n$, output $F_n$ mod $m$ (that is, the remainder of $F_n$ when divided by $m$).

**Input Format.** The input consists of two integers $m$ and $n$ given on the same line (separated by a space).

**Constraints.** $1 ≤ n ≤ 10^{18},2 ≤ m ≤ 10^{3}$.

**Output Format.** Output $F_n$ mod $m$.

**Sample 1.**
```
Input:
239 1000
Output:
161
```
$F_239 mod 1000 = 39 679 027 332 006 820 581 608 740 953 902 289 877 834 488 152 161 (mod 1 000) = 161$.
## Naive Algorithm



```python
def get_fibonacci_huge_naive(n, m):
    if n <= 1:
        return n

    previous = 0
    current  = 1

    for _ in range(n - 1):
        previous, current = current, previous + current

    return current % m
print(get_fibonacci_huge_naive(239, 1000))

```

    161


## Fast Algorithm
计算Fibonacci数直到m平方，因为pisano period不可能大于这个数。

因为最后一个数的后面是下一周期的0和1，那假设最后两个数分别是x和y，则(x+y)%m=0, (y+0)%m=1，所以 y=1, x=m-1为判别式。


```python
def get_fibonacci_huge_fast(n, m):
    A = [None]* (m**2)
    A[0] = 0
    A[1] = 1
    for i in range(2, m**2):
        A[i] = (A[i-1] + A[i-2]) % m
        if A[i] == 1 and A[i-1] == m-1:
            period = i+1
            break
    t = n % period
    return A[t] % m

print(get_fibonacci_huge_fast(239, 1000))
print(get_fibonacci_huge_fast(2816213588, 239))
```

    161
    151


# Last Digit of the Sum of Fibonacci Numbers
## 求斐波那契数之和的个位数
**Task.** Given an integer $n$, fnd the last digit of the sum $F_0 + F_1 + · · · + F_n$.

**Input Format.** The input consists of a single integer $n$.

**Constraints.** $0 ≤ n ≤ 10^{18}$.

**Output Format.** Output the last digit of $F_0 + F_1 + · · · + F_n$.

**Sample 1.**
```
Input:
3
Output:
4
```
$F_0 + F_1 + F_2 + F_3 = 0 + 1 + 1 + 2= 4$.
## Naive Algorithm



```python
def fibonacci_sum_naive(n):
    if n <= 1:
        return n

    previous = 0
    current  = 1
    sum      = 1

    for _ in range(n - 1):
        previous, current = current, previous + current
        sum += current

    return sum % 10
print(fibonacci_sum_naive(3))
print(fibonacci_sum_naive(100))
print(fibonacci_sum_naive(240))
```

    4
    5
    0


## Fast Algorithm

斐波那契数列的递归定义形式如下：

$$a_n= 
	\begin{cases}
	a_{n-1}+a_{n-2},\\
	1,& {n\leq 1}
	\end{cases}
$$   

斐波那契数列求和公式：

$$S_n= 2a_n+a_{n-1}-1$$




```python
def fibonacci_sum_fast(n):
    if n <= 1:
        return n

    previous = 0
    current  = 1

    for _ in range(n - 1):
        previous, current = current, (previous + current)

    return (2*current + previous - 1) % 10
print(fibonacci_sum_fast(3))
print(fibonacci_sum_fast(100))
print(fibonacci_sum_fast(240))
print(fibonacci_sum_fast(999999))
```

    4
    5
    0
    0


## More Fast Algorithm
去掉循环,用pisano period方法


```python
def fibonacci_sum_more_fast(n):
    if n <= 1:
        return n
    A = [None]* (100)
    A[0] = 0
    A[1] = 1
    for i in range(2, 100):
        A[i] = (A[i-1] + A[i-2]) % 10
        if A[i] == 1 and A[i-1] == 9:
            period = i+1
            break
    t = n % period
    if t == 0: 
        t = period-1
    return (2*A[t] + A[t-1] - 1) % 10
print(fibonacci_sum_more_fast(0))
print(fibonacci_sum_more_fast(3))
print(fibonacci_sum_more_fast(100))
print(fibonacci_sum_more_fast(240))
print(fibonacci_sum_more_fast(999999))
print(fibonacci_sum_more_fast(832564823476))
```

    0
    4
    5
    0
    0
    3


考虑到余数t==0的情况，A[t-1]值会出问题，因为是新一周期循环，值为0，所以，可以令t=period-1

# Last Digit of the Sum of Fibonacci Numbers Again
## 求斐波那契数之部分和的个位数
**Task.** Given two non-negative integers $m$ and $n$, where $m≤n$, fnd the last digit of the sum $F_m + F_{m+1} + · · · + F_n$.

**Input Format.** The input consists of two non-negative integers $m$ and $n$ separated by a space.

**Constraints.** $0 ≤m≤ n ≤ 10^{18}$.

**Output Format.** Output the last digit of $F_m + F_{m+1} + · · · + F_n$.

**Sample 1.**
```
Input:
3 7
Output:
1
```
$F_3 + F_4 + F_5 + F_6 + F_7 = 2 + 3 + 5 + 8 + 13 = 31$.
## Naive Algorithm


```python
def fibonacci_partial_sum_naive(from_, to):
    sum = 0

    current = 0
    next  = 1

    for i in range(to + 1):
        if i >= from_:
            sum += current

        current, next = next, current + next

    return sum % 10
print(fibonacci_partial_sum_naive(0, 1))
print(fibonacci_partial_sum_naive(1, 2))
print(fibonacci_partial_sum_naive(3, 7))
print(fibonacci_partial_sum_naive(5, 7))
print(fibonacci_partial_sum_naive(10, 10))
print(fibonacci_partial_sum_naive(10, 200))
```

    1
    2
    1
    6
    5
    2


## Fast Algorithm


```python
def fibonacci_partial_sum_fast(from_, to):
    if to == 0:
        return 0
    
    A = [None]* (100)
    A[0] = 0
    A[1] = 1
    for i in range(2, 100):
        A[i] = (A[i-1] + A[i-2])%10
        if A[i] == 1 and A[i-1] == 9:
            period = i+1
            break
            
    t = to % period
    f = (from_ - 1) % period
    
    if from_ == (0 or 1):
        return (2*A[t] + A[t-1] - 1) % 10
    
    return (2*(A[t]-A[f]) + A[t-1] - A[f-1]) % 10

print(fibonacci_partial_sum_fast(0, 1))
print(fibonacci_partial_sum_fast(1, 2))
print(fibonacci_partial_sum_fast(3, 7))
print(fibonacci_partial_sum_fast(5, 7))
print(fibonacci_partial_sum_fast(10, 10))
print(fibonacci_partial_sum_fast(10, 200))
```

    1
    2
    1
    6
    5
    2



```python
    A = [None]* (100)
    A[0] = 0
    A[1] = 1
    for i in range(2, to-1):
        A[i] = (A[i-1] + A[i-2]) % 10 
        if A[i] == 1 and A[i-1] == 9:
            period = i+1
            break
    t = (to-1) % period
    return (2*(A[t]-A[] + A[t-1] - 1) % 10
```

# Last Digit of the Sum of Squares of Fibonacci Numbers
## 求斐波那契数平方之和的个位数
**Task.** Compute the last digit of $F_0^2 + F_1^2 + · · · + F_n^2$.

**Input Format.** Integer n.

**Constraints.** $0 ≤ n ≤ 10^{18}$.

**Output Format.** The last digit of $F_0^2 + F_1^2 + · · · + F_n^2$.

**Sample 1.**
```
Input:
7
Output:
3
```
$F_0^2 + F_1^2 + · · · + F_7^2 = 0 + 1 + 1 + 4 + 9 + 25 + 64 + 169 = 273$.

## Naive Algorithm


```python
def fibonacci_sum_squares_naive(n):
    if n <= 1:
        return n

    previous = 0
    current  = 1
    sum      = 1

    for _ in range(n - 1):
        previous, current = current, previous + current
        sum += current * current

    return sum % 10
print(fibonacci_sum_squares_naive(7))
print(fibonacci_sum_squares_naive(73))
print(fibonacci_sum_squares_naive(211))
print(fibonacci_sum_squares_naive(239))
```

    3
    1
    1
    0


## Fast Algorithm
斐波那契数列各项相加的平方和为：

$$\sum_{n}a_n^2=a_1^2+a_2^2+\ldots + a_n^2=a_{n}a_{n+1}$$


```python
def fibonacci_sum_squares_fast(n):
    if n <= 1:
        return n
    A = [None]* (100)
    A[0] = 0
    A[1] = 1
    for i in range(2, 100):
        A[i] = (A[i-1] + A[i-2])%10
        if A[i] == 1 and A[i-1] == 9:
            period = i+1
            A[i+1] = (A[i-1] + A[i])%10
            break
            
    t = n % period

    return (A[t] * A[t+1]) % 10
print(fibonacci_sum_squares_fast(0))
print(fibonacci_sum_squares_fast(7))
print(fibonacci_sum_squares_fast(73))
print(fibonacci_sum_squares_fast(211))
print(fibonacci_sum_squares_fast(239))
print(fibonacci_sum_squares_fast(832564823476))
```

    0
    3
    1
    1
    0
    9


`break` 前面加了一行 `A[i+1] = (A[i-1] + A[i])%10` 的原因是，当余数为边界值period-1时，还要计算A[t+1]

参考：
 - [Coursera：algorithmic toolbox(week2)](https://www.coursera.org/learn/algorithmic-toolbox/home/week/2)
