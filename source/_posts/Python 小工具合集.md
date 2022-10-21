---
title: Python 小工具合集
date: 2022-06-19 23:57:21.412
updated: 2022-08-31 15:59:25.002
url: /?p=304
categories: 
- 默认分类
tags: 
- Python
---

## 1. 批量重命名

**source code**

```python 
from glob import glob
import os


def path(filepath):
    unsorted_filelist = glob(f"{filepath}")
    abs_path = filepath.rsplit("/", 1)[0] + "/"
    return unsorted_filelist, abs_path


def sort_filelist(li):
    """针对不同的文件名需要对 lambda 函数进行修改"""
    filelist = sorted(li, key=lambda x: x.split("/")[-1].split(" -")[0])
    return filelist


def process(li, path):
    """rename主函数"""
    print("processing...")
    for i in li:
        old_name = i
        swap = old_name.split(" - ")[-1]
        new_name = path + f"{swap}"
        print(f"{old_name}--------------->{new_name}")
        os.rename(old_name, new_name)
    print("process complite.")


if __name__ == '__main__':
    # 路径参数传入
    unsorted_filelist, abs_path = path("/Users/reido/Downloads/test/*.mp4")
    sorted_filelist = sort_filelist(unsorted_filelist)
    process(sorted_filelist, abs_path)

```

## 2. 跳板连接 mongodb

**requirements**

```python
pymongo==4.1.1
sshtunnel==0.4.0
```

**source code**

```python 
#!/usr/bin/python3
# -*- coding: utf-8 -*-

import pymongo
import atexit
from sshtunnel import SSHTunnelForwarder
import settings
server = SSHTunnelForwarder(
    (settings.SERVERIP,22),
    ssh_password=settings.SERVERPWD,
    ssh_username=settings.SERVERUSR,
    remote_bind_address=(settings.SERVERIP,settings.MONGODBPORT)
)
server.start()
print(server.is_active)
def shutdown():
    server.stop()
atexit.register(shutdown)

client = pymongo.MongoClient('127.0.0.1',server.local_bind_port)
print(client.server_info)
try:
    print(client.server_info())
except Exception:
    print("Unable to connect to the server.")
```

## 3. 批量图片转PDF

**requirements**

```python
PyMuPDF==1.20.0
```

**source code**

```python
#!/usr/bin/python3
# -*- coding: utf-8 -*-

import os
from glob import glob
import fitz

# 防止字符串乱码
os.environ['NLS_LANG'] = 'SIMPLIFIED CHINESE_CHINA.UTF8'
# /Users/reido/Documents/Developer/VSCode/combly_slides_download/slides_png

def parsepath(filepath):
    img_path = filepath.rsplit("/", 1)[0]
    img_suffix = "*." + filepath.rsplit("/", 1)[-1].split(".")[-1]
    return img_path, img_suffix


def pic2pdf(img_path, img_suffix):
    li = glob(f"{img_path}/{img_suffix}")
    sorted_list = sorted(li,key=lambda x:int(x.rsplit("/",1)[-1].split(".")[0]))
    doc = fitz.open()
    for i in sorted_list:
        img_doc = fitz.open(i)
        pdfbytes = img_doc.convert_to_pdf()
        pdf_name = str(i) + ".pdf"
        imgpdf = fitz.open(pdf_name,pdfbytes)
        doc.insert_pdf(imgpdf)
    doc.save('combined.pdf')
    doc.close()

if __name__ == '__main__':
    filepath = "/Users/reido/Documents/Developer/VSCode/combly_slides_download/slides_png/02 Art/0.png"
    img_path,img_suffix = parsepath(filepath)
    pic2pdf(img_path,img_suffix)
```

## 4. excel姓名批量转英文格式
**requirements**
```python
pandas==1.4.3
openpyxl==3.0.10
```
**source code**
```python
# /usr/local/bin/python3
# 文件位置
file_path = '~/Desktop/Workshop.xlsx'
# sheet name
sheet_name = 'Export'
# 指定读取Excel的引擎, 这里使用openpyxl, 不舍着引擎可能会出现 module not found error
engine = 'openpyxl'
# 需要更改为英文格式的行数
rows = 51
# 从excel的哪一行开始, 遵循从0开始
start_row = 1
# 需要更改为英文汉字的单元格col 从0开始
cell_col = 1
df = pd.read_excel(file_path, sheet_name=sheet_name, engine=engine)
for i in range(start_row, rows):
    zh_name = df.iloc[i, cell_col]
    name_list = lazy_pinyin(zh_name)
    last_name = name_list[0]
    first_name_list = name_list[1:]
    first_name = ""
    for x in first_name_list:
        first_name = first_name + x

    en_name = first_name + " " + last_name
    # 删除完整名字左右的空格
    en_name = en_name.title().lstrip()
    # 将中文替换为英文，并写在其后单元格内
    df.iloc[i,cell_col+1] = en_name
    print (en_name)
```


