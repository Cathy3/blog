---
title: Shell脚本编程(leetcode)
date: 2019-09-25
categories: 
	-   code
tags:  
        -   Shell
        -   LeetCode

mathjax: true
---
-   192. 统计词频

<!-- more -->

# 192. 统计词频
写一个 bash 脚本以统计一个文本文件 words.txt 中每个单词出现的频率。

为了简单起见，你可以假设：

-   words.txt只包括小写字母和 ' ' 。
-   每个单词只由小写字母组成。
-   单词间由一个或多个空格字符分隔。

示例:

假设 words.txt 内容如下：
```
the day is sunny the the
the sunny is is
```
你的脚本应当输出（以词频降序排列）：
```
the 4
is 3
sunny 2
day 1
```
说明:

-   不要担心词频相同的单词的排序问题，每个单词出现的频率都是唯一的。
-   你可以使用一行 Unix pipes 实现吗？

## 方法

1.  grep命令，文本搜索工具
    -   只输出文件中匹配到的部分 -o 选项
    -   正则表达式 -E 选项
    -   '[a-z]+' 指一个或多个字母
    -   结果是每行一个单词的多行文件
2.  sort命令 对找到的所有单词排序   
    -   将文件的每一行作为一个单位，相互比较，比较原则是从首字符向后，依次按ASCII码值进行比较，最后将他们按升序输出。
3.  uniq命令 去除重复行
    -   -c, --count //在每行前加上表示相应行目出现次数的前缀编号  
4.  再sort一下
    -   -n选项，“要以数值来排序”，
    -   -r 指降序排列
5.  awk命令就是将结果输出，两列颠倒位置即可


```
grep -oE '[a-z]+' words.txt | sort | uniq -c | sort -nr | awk '{print $2" "$1}' 
```
