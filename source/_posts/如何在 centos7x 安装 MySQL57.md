---
title: 如何在 centos7.x 安装 MySQL5.7
date: 2021-05-15 02:18:28.0
updated: 2021-11-09 07:57:05.595
url: /?p=24
categories: 
- 服务器
tags: 
- MySQL
---

## 1. 准备

作为IT开发人员,我们本着用旧不用新的原则,对于mysql,我们还是偏爱5.x版本. mysql5.7不同于8.0,后者可以直接通过yum直接安装,但是mysql5.7需要通过一些系列的配置之后才能正确安装.

```bash
wget https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm
```

## 2. 安装

```bash
yum install mysql80-community-release-el7-3.noarch.rpm

vim /etc/yum.repos.d/mysql-community.repo
# Enable to use MySQL 5.7
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

[mysql80-community]
name=MySQL 8.0 Community Server
baseurl=http://repo.mysql.com/yum/mysql-8.0-community/el/7/$basearch/
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql


yum install mysql -y
```

## 3. 启动

```bash
systemctl enable --now mysqld
systemctl start mysqld
```

## 4. 配置

```bash
# 修改密码
# mysql安装完成之后，会在/var/log/mysqld.log文件中给root生成了一个临时的默认密码。
cat /var/log/mysqld.log | grep password
# 使用该密码登陆root
# 进行密码修改
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';

# 修改权限
grant all privileges on *.* to root@'%' identified by 'password' with grant option;
flush privileges;

# 修改字符集
# 配置mysql默认编码为utf-8
# 修改/etc/my.cnf配置文件，在[mysqld]下添加编码配置
[client] 
default-character-set = utf8mb4 
[mysql] 
default-character-set = utf8mb4 
[mysqld] 
character-set-client-handshake = FALSE 
character-set-server = utf8mb4 
collation-server = utf8mb4_unicode_ci 
init_connect='SET NAMES utf8mb4'
```

## 5. 重启并查看字符集

```bash
systemctl restart mysqld
show variables like '%character%';
```