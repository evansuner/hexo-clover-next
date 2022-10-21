---
title: "SSH登录：WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!"
date: 2021-11-11 16:26:59.486
updated: 2021-11-11 16:26:59.486
url: /?p=35
categories: 
- 服务器
tags: 
- ssh
---

## 报错原因
远程登录 Linux 的时候需要验证公钥文件,本地家目录`~/.ssh/known_hosts`文件存储的就是针对特定域名的公钥,当我们重装了远程 Linux 系统,而恰恰没有更新公钥文件时,就会报出当下错误.
## 报错信息
```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED    @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
SHA256: 6zKiuPSPE90dB6PNCj1fICgA1DTrTxPFiVbl82+sSOE
Please contact your system administrator.
Add correct host key in /c/Users/xuliugen/.ssh/known_hosts to get rid of this message.
Offending RSA key in /c/Users/xuliugen/.ssh/known_hosts:4
RSA host key for 119.19.19.19 has changed and you have requested strict checking.
Host key verification failed.
```
## 解决方式
```bash
# 删除当前ip地址下的公钥即可
ssh -R 121.5.117.127
```
此时已经可以正常远程登录
```bash
  ~ ssh root@121.5.117.127
The authenticity of host '121.5.117.127 (121.5.117.127)' can't be established.
ED25519 key fingerprint is SHA256:33aY2Bd1jzz/IjYpG3VnSf6B4mqJuuDBH20Hm9/KMmw.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '121.5.117.127' (ED25519) to the list of known hosts.
root@121.5.117.127's password: 
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Thu Nov 11 15:55:16 2021 from 188.163.102.226
➜  ~ 
