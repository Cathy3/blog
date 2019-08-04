---
title: csv文件与字典之间的转换
date: 2019-08-3
categories: 
	- code
tags:  
        - python
---

-   csv文件转换为字典
    -   第一行为key，其余行为value
    -   每一行为key,value的记录
-   字典转换为csv文件
    -   第一行为key，其余行为value
    -   每一行为key,value的记录
<!-- more -->

# 字典转换为csv文件
-   每一行为key,value的记录
-   第一行为key，其余行为value
-   输出列表字典

# 每一行为key,value的记录

```python
def dict2csv(dict,file):
    with open(file,'w') as f:
        w=csv.writer(f)
        # write each key/value pair on a separate row
        w.writerows(dict.items())
```

# 参考
-   [csv文件与字典，列表等之间的转换小结【Python】](https://segmentfault.com/a/1190000007092479#articleHeader1)