---
title: 记录Python 几个常用内置库
date: 2022-07-07 21:17:40.74
updated: 2022-07-07 21:17:40.74
url: /?p=305
categories: 
- 默认分类
tags: 
- Python
---

## heapq
**用于返回列表中最大或最小的几个元素**
```python
import heapq

l1 = [21,22,23,24,25,24,26,27,28]
# 返回最大的三个元素
print(heapq.nlargest(3,l1))  # [28, 27, 26]
# 返回最小的三个元素
print(heapq.nsmallest(3,l1))  # [21, 22, 23]

l2 = [
    {"最新价": 43.03, "涨跌幅": 38.85, "代码": "688322", "名称": "N奥比-UW", },
    {"最新价": 426.0, "涨跌幅": 15.68, "代码": "688063", "名称": "派能科技", },
    {"最新价": 93.51, "涨跌幅": 15.42, "代码": "688700", "名称": "东威科技", },
    {"最新价": 34.15, "涨跌幅": 14.02, "代码": "688676", "名称": "金盘科技", }
]
print(heapq.nlargest(2, l2, key=lambda x: x["最新价"]))
print(heapq.nsmallest(2, l2, key=lambda x: x["最新价"]))
```
## itertools
**用于生成各种各样的迭代器**
```python
import itertools
# 产生 ABCD 的全序列
for value in itertools.permutations("ABCD"):
    print(value)

# 产生 ABCDE 的五选三组合
for value in itertools.permutations("ABCDE",3):
    print(value)

# 产生 ABCD 与123的笛卡尔积
for value in itertools.product("ABCD","123"):
    print(value)

# 产生 ABCD 的无限循环序列
it = itertools.cycle(('A','B','C','D'))
for i in range(5):
    print(next(it))
```

## uuid
**可以帮助我们生成全局唯一标识符（Universal Unique IDentity）。该模块提供了四个用于生成UUID的函数，分别是：**
+ uuid1()：由MAC地址、当前时间戳、随机数生成，可以保证全球范围内的唯一性。
+ uuid3(namespace, name)：通过计算命名空间和名字的MD5哈希摘要（“指纹”）值得到，保证了同一命名空间中不同名字的唯一性，和不同命名空间的唯一性，但同一命名空间的同一名字会生成相同的UUID。
+ uuid4()：由伪随机数生成UUID，有一定的重复概率，该概率可以计算出来。
+ uuid5()：算法与uuid3相同，只不过哈希函数用SHA-1取代了MD5。

由于uuid4存在概率型重复，那么在真正需要全局唯一标识符的地方最好不用使用它。在分布式环境下，uuid1是很好的选择，因为它能够保证生成ID的全局唯一性。
```python
import uuid
for i in range(4):
    print(uuid.uuid1().hex)
    
"""
79afe978fdf611ec82a3acde48001122
79afebdafdf611ec82a3acde48001122
79afec34fdf611ec82a3acde48001122
79afec7afdf611ec82a3acde48001122
"""
```