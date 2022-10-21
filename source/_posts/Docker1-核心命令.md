---
title: Docker1-核心命令
date: 2022-01-04 05:38:31.861
updated: 2022-01-06 00:56:18.244
url: /?p=101
categories: 
- 服务器
tags: 
- docker
---

企业级项目
> 高可用 高并发 高性能 安全 监控
> 高可用、高并发 --> 解决方案：服务器配置 --> 集群
> ```bash
> # 一、以12306网站为例
> 节假日 --> 购票需求量很高
> 平时 --> 购票需求量很低
> 12306 高并发的问题 节假日并发量是非常高的 QPS：10万 --> 100个tomcat
> 12306 平时并发量很低 --> 10个tomcat
> 解决高并发： 能够设计保证能够并行处理多个请求的能力。
> 集群： 多个服务器来支撑服务的运行
> 服务忙闲不均、资源浪费的问题
> 如何动态地解决这些问题呢？
> 
> # 二、高可用部署
> 高可用：系统经过专门的设计，减少服务的停工时间，保证其服务的高度可用性不断对服务器进行扩容
> 部署100个：
>   部署单个： 搜搜引擎（Solr）+ Mysql + Tomcat + Nginx
> 问题： 需要花费大量人力物理，需要部署多个服务器
> 
> # 三、服务器的兼容性（可用性）
> 一个企业级项目，需要部署多个Tomcat
> 一个Tomcat就是一个微服务
> 一个服务器可能多个应用服务（Tomcat），不同的部门，不同的团队。如果某个部门开发的项目出现意外，例如：死循环，无限制的创建对象到内存中国年，容易将操作系统的资源全部占用，就可能因为一个团队的代码质量问题，到执政个服务器发生崩溃现象，因为所有的tomcat都部署在同一个服务器，导致其他服务全部出现问题。
> 
> # 四、部署
> 为了保证线上部署顺利
> 保证： 开发环境 <==> 生产环境 保持一致
> ```
> 解决方案：就是Docker
## 一、Docker介绍
### 什么是Docker
Docker是一个开元的应用容器引擎，基于go开发并遵从Apache2.0协议开源。
**Docker可以让开发者打包他们的应用以及一路来包到一个轻量级、可移植的容器中，然后发布到任何流行的Linux机器上，也可以是先虚拟化**
Docker容器完全使用沙箱机制，互相不会有任何接口（类似iPhone的app），更重要的是容器性能开销极低。
### Docker的应用场景
Web应用的自动化打包和发布
自动化测试和持续集成、发布
从头编译或者扩展先用的Openshift或Cloud Foundry平台来来搭建自己的PaaS环境
**云计算+大数据时代**
```bash
开发一套应用程序代码、服务器、安装开发环境（JDK|Tomcat|Mysql|Nginx|RabbitMQ|Redis|Solr）
IaaS: (Infrastructure-as-a-Service)(基础设施即服务)
PaaS: (Platform-as-a-Service)(平台即服务)
SaaS: (Software-as-a-Service)(软件即服务)
```
### Docker和虚拟器总结
![16410449362106](https://mweb-cdn.reidosann.top/mweb/16410449362106-20220101-16410449362138.jpg)
```bash
名词解释：
infrastructure （基础服务）硬件 Host OS 主机操作系统
VM 虚拟机  Hypervisor 虚拟层程序
```

## 二、Docker架构
### 简介
Docker使用客户端-服务器（C/S）架构模式，使用远程API来管理和创建Docker容器。
Docker容器通过Docker镜像来创建
容器与镜像之间的关系类似于面向对象编程中的对象和类
### Docker基本概念
Docker包括三个基本概念
+ 镜像（Image）
+ 容器（Container）
+ 仓库（Repository）
理解这三个概念，就理解了Docker的整个生命周期了
### Docker引擎
引擎组件的流程如下图所示：
![16410458671903](https://mweb-cdn.reidosann.top/mweb/16410458671903-20220101-16410458671916.jpg)
### Docker仓库
+ **公有Docker Registry**
+ **私有Docker Registry**

### Docker安装
**更换源(centos)**
```bash
# 官方源
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
# 阿里云源
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

```
### Docker镜像加速器
**安装**
[Docker官方文档](https://docs.docker.com/engine/install/centos/)
**国内云服务商提供的加速器**
+ [Docker官方提供的中国registry mirror](https://registry.docker-cn.com)
+ 阿里云加速器
    +   进入阿里云镜像加速页面（需要登录）：https://cr.console.aliyun.com/undefined/instances/mirrors
    +   获取如下加速器地址：https://XXXXXX.mirror.aliyuncs.com
+ [腾讯镜像源加速](https://mirror.ccs.tencentyun.com)
```bash
# 配置方式(没有则创建)：
sudo vim /etc/docker/daemon.json

{
    "registry-mirrors":["https://mirror.ccs.tencentyun.com"]
}

sudo systemctl daemon-reload
sudo systemctl restart docker
```
### Docker镜像
+ 搜索镜像 `docker search tomcat`
+ 列出镜像 `docker images // docker images ls`
+ 获取镜像 `docker pull [选项] [Docker Register 地址[:端口号]/]镜像名称[:标签]`
    ```bash
    eg:
    docker pull halohub/halo:1.4.16
    docker pull tomcat
    # 标签就是版本号 不添加则默认使用latest
    ```
+ 删除本地镜像 `docker rmi [选项] 镜像ID`
+ docker辅助命令
    + 查看本地镜像ID `docker images -q`
    + 查看一个镜像的制作历程 `docker history 镜像名称`
+ 保存镜像
    + 备份本地仓库的镜像
        ```bash
        保存：
        docker save -o tomcat.tar 镜像名称
        导入：
        方案一：
        docker load -i tomcat.tar
        方案二：
        docker load < tomcat.tar
        ```
### Docker容器
+ 查看容器状态
    ```bash
    docker ps
    docker ps -a
    docker container ls
    docker container ls -a
    ```
+ 启动容器 `docker run 参数 镜像名称:tag 执行的命令`   
    **常用参数**
    + -i 保持和docker容器内的交互，启动容器时，运行的命令结束后，容器依然存活，没有退出（默认是会退出，即停止）
    + -t 胃痛起的标准输入虚拟一个 tty
    + -d 后台运行容器
    + --name 给容器起一个自定义名称
    + -p 宿主机内部端口
    + -v 宿主机内部运行位置  **数据卷**
    + --restart=unless-stopped 重启方式
    + --rm 容器停止后自动销毁 
    ```bash
    eg:
    docker run -it -d --name halo -p 8090:8090 -v ~/.halo:/root/.halo --restart=unless-stopped halohub/halo:1.4.16
    ```
+ 停止容器
    + `docker stop name/id`
    + 停止所有容器 `docker stop $(docker ps -a -q)`
+ 启动容器 `docker start name/id`
+ 重启容器 `docker restart name/id`
+ 删除容器 `docker rm name/id`
+ 进入容器 `docker exec -it 容器名 bash`
+ 在宿主机和容器之间交换文件
    ```bash
    # 与宿主机之间交换文件使用docker cp命令，用法如下：
    docker cp [options] CONTAINER:PATH LOCALPATH #从容器中复制到宿主机
    docker cp [options] LOCALPATH|- CONTAINER:PATH #从宿主机中复制文件
    
    eg：
    # 从宿主机中复制一张图片到容器的指定目录下
    docker cp anime.png tomcat-8080:/usr/local/tomcat/webapps/ROOT
    
    # 从容器的指定目录复制出文件，本地修改后再复制回去
    docker ps tomcat-8080:/usr/local/tomcat/webapps/index.html /root
    ```
### Docker查看日志
```bash
docker logs name/id
docker logs -f -t --since="2018-12-1" --tail=10 tomcat-8080
```
+ --since 指定输出日志的开始时间
+ -f 查看实时日志
+ -t 查看日志产生的日期
+ --tail=10 查看最后的10条日志
+ tomcat-8080 容器名称

### 数据卷
**应用数据卷**
```bash
# 当你映射数据卷的时候，如果数据卷不存在Docker会自动帮你创建
docker run -v 数据卷名称:容器内路径 镜像ID
# 直接指定一个路径作为数据卷的存储位置
docker run -v 路径:容器内部的路径 镜像
```




