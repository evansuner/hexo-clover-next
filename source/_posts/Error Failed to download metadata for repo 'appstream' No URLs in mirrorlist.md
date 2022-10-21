---
title: "Error: Failed to download metadata for repo 'appstream': No URLs in mirrorlist"
date: 2022-06-14 11:15:09.418
updated: 2022-06-20 17:03:04.689
url: /?p=273
categories: 
- 服务器
tags: 
- centos
---

我试图用yum在CentOS上下载java，这是我在Dockerfile中指定的。在下载了CentOS的镜像后，运行时出现了这个错误！还需要提到的是，我的服务器实例是CentOS8.2!
```bash
Step 2/9 : RUN yum install java -y
 ---> Running in 39fc233aa965
CentOS Linux 8 - AppStream                      184  B/s |  38  B     00:00
Error: Failed to download metadata for repo 'appstream': Cannot prepare internal mirrorlist: No URLs in mirrorlist
The command '/bin/sh -c yum install java -y' returned a non-zero code: 1
```
### Method
```bash
wget 'http://mirror.centos.org/centos/8-stream/BaseOS/x86_64/os/Packages/centos-gpg-keys-8-3.el8.noarch.rpm'

sudo rpm -i 'centos-gpg-keys-8-3.el8.noarch.rpm'

dnf --disablerepo '*' --enablerepo=extras swap centos-linux-repos centos-stream-repos

sudo dnf distro-sync
# Finally we can use yum as usual
```