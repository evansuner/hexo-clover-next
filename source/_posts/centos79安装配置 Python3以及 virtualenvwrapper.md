---
title: centos7.9安装配置 Python3以及 virtualenvwrapper
date: 2021-08-20 02:18:30.0
updated: 2021-11-09 08:01:48.217
url: /?p=28
categories: 
- 服务器
tags: 
- Python
- virtualenvwrapper
---

CentOS 7.9并没有预装python3 所以我们需要自行安装

这里我们只提供最简便的方法 安装 python3.6.8

```bash
yum install python36 -y
```

就此安装结束

---

**然并luan**

How to use virtualenvwrapper on linux?

## 1. 准备

```bash
pip3 install virtualenv
pip3 install virtualenvwrapper
mkdir ~/.virtualenvs
```

## 2. 查找位置

```bash
whereis python3
# /usr/bin/python3
whereis virtualenvwrapper.sh
# /usr/local/bin/virtualenvwrapper.sh
```

## 3. 配置`.bashrc`文件

```bash
vim ~/.bashrc
# 新增virtualenvwrapper配置
VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export WORKON_HOME=~/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```

## 4. 编译`.bashrc`文件

```bash
source ~/.bashrc
```
