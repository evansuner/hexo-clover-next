---
title: Mac删除launchpad无用图标
date: 2022-01-11 19:02:34.37
updated: 2022-01-11 19:02:34.37
url: /?p=141
categories: 
- MacOS
tags: 
- 使用技巧
---

```bash
➜  ~ cd /private/var/folders 
➜  folders sudo find ./ -name com.apple.dock.launchpad
# 找到的文件夹不同的机器可能不同
➜  folders cd .//1w/6np04qmn18ldkwzym9f77mw80000gn/0/com.apple.dock.launchpad
➜  com.apple.dock.launchpad cd db
# 这里的title填写你需要删除的应用图标名称
➜  db sqlite3 db "delete from apps where title='<Cineware>';"&&killall Dock
```