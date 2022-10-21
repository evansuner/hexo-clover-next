---
title: 有关mysql8.0 授权时没有权限问题
date: 2021-09-25 02:18:25.0
updated: 2021-11-09 07:55:19.847
url: /?p=22
categories: 
- 服务器
tags: 
- mysql8.0
---

## 解决mysql8.0报错: ERROR 1410 (42000): You are not allowed to create a user with GRANT

### 1. 今天在到手的Mac上安装了mysql8.x, 想着从Windows上导入一点数据到mac上来,结果在权限分配的时候报错:

```bash
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'IDENTIFIED BY 'mypassword' WITH GRANT OPTION' at line 1
```

### 2. 查完mysql8.x的资料以后得知, mysql8.x分配全新啊不能带密码,需要隐式创建账号了, 需要先创建账号,之后才能设置权限:

```bash
grant all privileges on *.* to root@'%' with grant option;
```

### 3. 但是到了这里之后有曝出一个新的错误

```bash
mysql>  grant all privileges on *.* to 'username'@'%' with grant option; 
ERROR 1410 (42000): You are not allowed to create a user with GRANT
```

这里说我没有权限使用grant,但是我明明是用的是root用户

### 4. 尝试修改 mysql数据库中的user表中的host字段

```bash
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> update user set host="%" where user='root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> grant all privileges on *.* to root@'%';
Query OK, 0 rows affected (0.00 sec)

flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

### 4. 至此, 还没有结束!!!

我们使用navicat连接数据库的时候还是会出现报错

![image-20210914012003106](https://i.loli.net/2021/09/14/ldGhVesmfnHXOKA.png)

这个报错的原因还是在于mysql8.x的安全性问题

Mysql8.x使用更加安全的密码加密方式,这个和navicat不一样,所以我们需要将mysql进行如下修改:

```bash
mysql> alter user root identified with mysql_native_password by 'mypassword';
Query OK, 0 rows affected (0.00 sec)
```

再次尝试使用navicat连接本地数据库

![image-20210914012706285](https://i.loli.net/2021/09/14/4xvlLp8RGrXVZPk.png)

连接成功!!!