---
title: OptionBinding with id "failovermethod" does not exist
date: 2022-01-04 22:26:14.888
updated: 2022-01-04 22:26:14.888
url: /?p=103
categories: 
- 服务器
tags: 
- centos
---

## 报错信息：
```bash
~ yum update
Invalid configuration value: failovermethod=priority in /etc/yum.repos.d/CentOS-Epel.repo; Configuration: OptionBinding with id "failovermethod" does not exist
```
## 解决方式
```bash
~ vim /etc/yum.repos.d/CentOS-Epel.repo

~ # 注释failovermethod=priority
[epel]
name=EPEL for redhat/centos $releasever - $basearch
baseurl=http://mirrors.tencentyun.com/epel/$releasever/Everything/$basearch
# failovermethod=priority
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8
```
重新执行`yum update`，问题解决！
