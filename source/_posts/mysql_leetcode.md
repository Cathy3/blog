---
title: 数据库MySQL(leetcode)
date: 2019-09-27
categories: 
	-   code
tags:  
        -   数据库
        -   MySQL
        -   LeetCode

mathjax: true
---
-   175. 组合两个表

<!-- more -->

# 175. 组合两个表
表1: Person
```
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId 是上表主键
```
表2: Address
```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId 是上表主键
```
编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：
```
FirstName, LastName, City, State
```

## 方法
LEFT JOIN 关键字从左表（table1）返回所有的行，即使右表（table2）中没有匹配。如果右表中没有匹配，则结果为 NULL。

```sql
SELECT FirstName, LastName, City, State FROM Person LEFT JOIN Address
    ON Person.PersonId = Address.PersonId
```
