---
title: Docker4-Docker图形化工具
date: 2022-01-06 01:24:55.237
updated: 2022-01-06 01:24:55.237
url: /?p=105
categories: 
- 服务器
tags: 
- docker
---

> docker图形页面管理工具常用有三种：
> + DockerUI
> + Portainer（最受欢迎）
> + Shipyard

## 搭建Portainer图形化页面
```bash
# 搜索portainer镜像
➜  ~ docker search portainer
# 拉取镜像
➜  ~ docker pull portainer/portainer
# 启动镜像
➜  ~ docker run -itd --name portainerUI -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer 
# 开放防火墙端口 浏览器访问
http://ip:9000/
```
![16414033224786](https://mweb-cdn.reidosann.top/mweb/16414033224786-20220105-16414033224796.jpg)

使用单机版安装：

![16414033780571](https://mweb-cdn.reidosann.top/mweb/16414033780571-20220105-16414033780581.jpg)
点击`Connect`即可完成安装！

