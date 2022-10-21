---
title: Docker配置镜像加速
date: 2022-01-11 23:18:49.606
updated: 2022-01-11 23:18:49.606
url: /?p=171
categories: 
- 服务器
tags: 
- docker
---

在国内访问Docker的镜像仓库速度相当的慢，Docker官方和国内的大型云服务商给我们提供了便利。

## 修改或创建`/etc/docker/daemon.json`文件   
一般来说新安装的docker，在etc下是存在docker文件夹的，并且docker文件夹下存在一个名为key.json的文件，我们要做的就是创建一个守护进程配置文件，如果哦不存在就需要手动穿件`/etc/docker`文件夹
```bash
cd /etc/docker
vim daemon.json
# 写入配置项，保存并退出
{
    "registry-mirrors": [
        "https://1nj0zren.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://f1361db2.m.daocloud.io",
        "https://registry.docker-cn.com"
    ]
}

sudo systemctl daemon-reload
sudo systemctl restart docker
```
## 检查是否配置成功
```bash
docker info
# 如果info信息的最下面有以下信息，这说明配置成功

 Registry Mirrors:
  https://1nj0zren.mirror.aliyuncs.com/
  https://docker.mirrors.ustc.edu.cn/
  http://f1361db2.m.daocloud.io/
  https://registry.docker-cn.com/
```
