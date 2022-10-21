---
title: csv在Python中的使用
date: 2022-02-11 03:42:02.498
updated: 2022-07-18 21:08:10.857
url: /?p=213
categories: 
- 后端
tags: 
- Python
---

csv 模块实现类以读取和写入 CSV 格式的表格数据。 它允许程序员说“以 Excel 首选的格式写入此数据”或“从 Excel 生成的此文件中读取数据”，而无需知道 Excel 使用的 CSV 格式的确切细节。 程序员还可以描述其他应用程序可以理解的 CSV 格式或定义自己的专用 CSV 格式。

csv 模块的 reader 和 writer 对象读取和写入序列。 程序员还可以使用 DictReader 和 DictWriter 类以字典形式读取和写入数据。

## csv写操作
+ 方式一   
```python
import csv
with open('thesis.csv', 'w', encoding="utf-8") as fp:
    writer = csv.writer(fp)
    # writer.writerows([["title","cid"],["图像分类",445609486],["音乐分类",445609205]])
    writer.writerow(["title","cid"])
    writer.writerow(["图像分类",445609486)
    writer.writerow(["音乐分类",445609205])
  ```
+ 方式二

```Python
import csv
with open('names.csv', 'w', newline='',encoding='utf-8') as fp:
    fieldnames = ['first_name', 'last_name']
    writer = csv.DictWriter(fp, fieldnames=fieldnames)

    writer.writeheader()
    writer.writerow({'first_name': 'San', 'last_name': 'Zhang'})
    writer.writerow({'first_name': 'Si', 'last_name': 'Li'})
    writer.writerow({'first_name': 'Wu', 'last_name': 'Wang'})
```

## csv读操作
+ 方式一
```python
import csv
with open('names.csv', newline='', encoding='utf-8') as f:
    reader = csv.reader(f)
    for row in reader:
        print(row[0],row[1])
```
+ 方式二
```python
import csv
with open('names.csv', newline='') as fp:
    reader = csv.DictReader(fp)
    for row in reader:
        print(row['first_name'], row['last_name'])
```
