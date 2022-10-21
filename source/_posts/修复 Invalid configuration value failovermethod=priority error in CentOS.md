---
title: "修复 Invalid configuration value: failovermethod=priority error in CentOS"
date: 2022-06-14 11:11:01.305
updated: 2022-06-20 17:03:19.012
url: /?p=272
categories: 
- 服务器
tags: 
- centos
---

最近在 CentOS 使用`dnf`包管理器进行更新操作的时候会出现一下错误：
```bash
Invalid configuration value: failovermethod=priority in /etc/yum.repos.d/fedora.repo; Configuration: OptionBinding with id "failovermethod" does not exist
```
在进一步检查这个错误后，我发现这个参数必须从保存 CentOS Linux 中安装的不同软件包的存储库信息的不同文件中删除。下面是删除它的方法：
### Method
+ 使用 `sed` 进行备份
```bash
sudo sed -iBAK '/^failovermethod=/d' /etc/yum.repos.d/*.repo
```
+ 使用 `sed` 将出现问题的repo 文件中的 "failovermethod" 删除
```bash
sudo sed '/^failovermethod=/d' /etc/yum.repos.d/*.repo
```
+ 一旦你使用了这种方法，你可以运行`dnf`来检查错误是否再次出现。
```bash
dnf upgrade
```