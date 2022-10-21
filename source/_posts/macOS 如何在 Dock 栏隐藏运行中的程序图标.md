---
title: macOS 如何在 Dock 栏隐藏运行中的程序图标
date: 2021-11-11 16:47:57.04
updated: 2021-12-17 19:43:09.343
url: /?p=36
categories: 
- MacOS
tags: 
- 使用技巧
---

### 1.打开 Finder，选择“应用程序”；
### 2.选中想要隐藏的APP，右键“显示包内容”；
### 3. Contents -&gt; Info.plist;
### 4.在内容标签中添加以下内容
```xml
<key>LSElement</key>
<string>1</string>
```
![2019082722205664.png](https://img-cdn.reidosann.top///2019082722205664-c63d2dda111f4382bee6c471005058ef_1639741285769.png) 
### 5.重启应用，如果图标还在，则右击选择“从程序坞中移除”
                