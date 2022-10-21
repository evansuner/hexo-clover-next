---
title: 使用源码升级 openssh
date: 2021-11-02 03:18:30.0
updated: 2021-11-09 08:03:27.719
url: /?p=29
categories: 
- 服务器
tags: 
- openssh
---

## 1. 准备

使用源码安装OpenSHH需要zlib, openssl, openssh

```bash
mkdir ~/sshupdate
cd ~/sshupdate
wget https://www.openssl.org/source/openssl-1.1.1k.tar.gz
wget http://zlib.net/zlib-1.2.11.tar.gz
wget https://openbsd.hk/pub/OpenBSD/OpenSSH/portable/openssh-8.6p1.tar.gz
```

## 2. 安装zlib

```bash
tar -zxvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure --prefix=/usr/local/zlib
make && make install
```

## 3. 安装openssl

```bash
tar -zxvf openssl-1.1.1k.tar.gz
cd openssl-1.1.1k
./config --prefix=/usr/local/ssl -d shared
make && make install

ln -s /usr/local/ssl/bin/openssl /usr/bin/openssl
ln -s /usr/local/ssl/include/openssl /usr/include/openssl
ln -s /usr/local/ssl/lib/libssl.so.1.1 /usr/lib64/libssl.so.1.1
ln -s /usr/local/ssl/lib/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
```

## 4. 安装openssh

```bash
tar -zxvf openssh-8.6p1.tar.gz
cd openssh-8.6p1

./configure --prefix=/usr/local/openssh --with-zlib=/usr/local/zlib --with-ssl-dir=/usr/local/ssl
# configure: error: *** zlib.h missing - please install first or check config.log *** 报错解决
yum install zlib-devel

make && make install

# 卸载由yum安装的openssh
yum remove openssh -y

# 配置sshd_config
vim /usr/local/openssh/etc/sshd_config
PermitRootLogin yes
PubkeyAuthentication yes
PasswordAuthentication yes

cp /usr/local/openssh/etc/sshd_config /etc/ssh/sshd_config
cp /usr/local/openssh/sbin/sshd /usr/sbin/sshd
cp /usr/local/openssh/bin/ssh /usr/bin/ssh # 或者 ln -s /usr/local/openssh/bin/ssh /usr/bin/ssh
cp /usr/local/openssh/bin/sftp /usr/bin/sftp # ln -s /usr/local/openssh/bin/sftp /usr/bin/sftp
cp /usr/local/openssh/bin/ssh-add /usr/bin/ssh-add # ln -s /usr/local/openssh/bin/ssh-add /usr/bin/ssh-add
cp /usr/local/openssh/bin/ssh-keygen /usr/bin/ssh-keygen # ln -s /usr/local/openssh/bin/ssh-keygen /usr/bin/ssh-keygen
cp /usr/local/openssh/bin/ssh-keyscan /usr/bin/ssh-keyscan # ln -s /usr/local/openssh/bin/ssh-keyscan /usr/bin/ssh-keyscan

cp /usr/local/openssh/etc/ssh_host_ecdsa_key.pub /etc/ssh/ssh_host_ecdsa_key.pub

# 启动
systemctl start sshd
```



> 参考:
>
> openssl安装参考文档 [How to solve openssl error when loading shared libraries libssl.so.1.1](https://www.bswen.com/2018/11/others-Openssl-version-cause-error-when-loading-shared-libraries-libssl.so.1.1.html)
>
> openssh安装参考文档 [Centos7升级openssh到最高版本](https://blog.csdn.net/xyy1028/article/details/104787689)