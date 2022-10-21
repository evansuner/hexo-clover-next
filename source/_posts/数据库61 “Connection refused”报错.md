---
title: 数据库61 “Connection refused”报错
date: 2021-12-01 07:51:21.3
updated: 2021-12-10 23:44:03.834
url: /?p=65
categories: 
- 服务器
tags: 
- MySQL
-  远程登录
---

## 腾讯云-云服务器远程登录MySQL 出现Can‘t connect to MySQL server on ‘xxx‘ (61 “Connection refused”)

在腾讯服务器上已经配置好了允许任何 ip 登录，但是使用 navicat 实施远程登录的时候依旧报错，错误信息 Can‘t connect to MySQL server on ‘xxx‘ (61 “Connection refused”)

### 服务器信息：

![image-20211201011109006](https://i.loli.net/2021/12/01/4tIrRzwJFLWMYHp.png)

### navicat 报错：

![WX20211201-011446@2x](https://i.loli.net/2021/12/01/jdfEUrhegyst3pL.png)

远程连接被拒接

### 解决方式：

这里使用关闭端口防火墙的方式

```bash
~ systemctl stop firewalld.service 
~ systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since Wed 2021-12-01 07:18:02 CST; 17s ago
     Docs: man:firewalld(1)
  Process: 2284187 ExecStart=/usr/sbin/firewalld --nofork --nopid $FIREWALLD_ARGS (code=exited, status=0/SUC>
 Main PID: 2284187 (code=exited, status=0/SUCCESS)
...
```

但是，没有防火墙的保护服务器是相当不安全的，这里我们可以使用 iptables 代替

#### 安装 iptables

第一步：

先禁用 firewalld

```bash
~ systemctl disable firewalld.service 
Removed /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed /etc/systemd/system/multi-user.target.wants/firewalld.service.
```

第二步：

安装并启动 iptables-services，设置开机自启

```bash
~ dnf install -y iptables-services
~ systemctl start iptables.service 
~ systemctl enable iptables.service 
Created symlink /etc/systemd/system/multi-user.target.wants/iptables.service → /usr/lib/systemd/system/iptables.service.
```

第三步：

更改并保存 iptables 规则,重启 systemctl

```bash
# 备份 iptables 文件
~ cp -a /etc/sysconfig/iptables /etc/sysconfig/iptables.bak
# 设置 INPUT 方向的请求全部拒绝
~ iptables -P INPUT DROP 
# 开放所需要的端口
~ iptables -I INPUT -p tcp --dport 80 -m state --state NEW -j ACCEPT
~ iptables -I INPUT -p tcp --dport 3306 -m state --state NEW -j ACCEPT
# 保存规则
~ iptables-save > /etc/sysconfig/iptables
# 重启 systemctl
~ systemctl reboot
```




