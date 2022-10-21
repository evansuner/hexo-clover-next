---
title: Docker从入门到精通
date: 2021-10-22 02:18:28.0
updated: 2021-11-09 07:58:53.603
url: /?p=26
categories: 
- 服务器
tags: 
- docker
---

简介: docker是一个开源项目,基于go语言实现,遵循Apache2.0协议. 其目标是实现轻量级的操作系统虚拟化解决方案. docker的基础是Linux容器等技术. 在LXC的基础上docker的容器就像是操作一个快速轻量级的虚拟机一样简单. 以及依赖docker可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中,然后发不到流行的Linux机器上,也可以实现虚拟化开发. 容器是完全使用沙箱机制,相互之间不会有任何接口, 更重要的是容器性能开销极低.

## 一、为什么使用Docker

### 1. Docker容器虚拟化的好处

在云时代，开发者创建的应用必须要能很方便地在网络上传播，也就是说应用必须脱离底层物理硬件的限制；同时必须满足“任何时间任何地点”可获取可使用的特点。因此，开发者们需要一种新型的创建分布式应用程序的方式，快速分发部署，而这正是Docker所能够提供的最大优势。Docker提供了一种更为聪明的方式，通过容器来打包应用、解耦应用和运行平台。这意味着迁移的时候，只需要在新的服务器上启动需要的容器就可以了，无论新旧服务器是否是同一类别的平台。这无疑帮助我们节约了大量的宝贵时间，并降低部署过程出现问题的风险。

### 2. Docker在开发和运维中的优势

对于开发和运维人员来说，最梦寐以求的效果可能就是一次创建和配置，之后可以在任意地方、任意时间让应用正常运行，而Docker恰恰可以实现这一中级目标。具体来说，在开发和运维过程中，Docker具有以下几个方面的优势：

**更快的交付和部署：**使用Docker，开发人员可以使用镜像来快速构建一套标准的开发环境；开发完之后，测试和运维人员可以直接使用完全相同的环境来部署代码。只要是开发测试过的代码，就可以确保在生产环境无缝运行。Docker可以快速创建和删除容器，实现快速迭代，节约开发、测试及部署的时间。

**更高效的利用资源：**运行Docker容器不需要额外的虚拟化管理程序的支持，Docker是内核级的虚拟化，可以实现更高的性能，同时对资源的额外需求很低，与传统的虚拟机方式相比，Docker的性能要提高1 ü ~ 2个数量级。

**更轻松的迁移和扩展：**Docker容器几乎可以在任意的平台上运行，包括物理机、虚拟机、公有云、私有云、个人电脑等等，同时支持主流的操作系统发行版本。这种兼容性能让用户可以在不同的平台之间轻松的迁移应用。

**更轻松的管理和更新：**使用Dockerfile，只需要小小的配置修改，就可以替代以往大量的更新工作。所有的修改都以增量的方式被分发和更新，从而实现自动化并且高效的容器管理。

### 3. Docker与虚拟机的比较

 作为一种轻量级的虚拟化方式，Docker在运行应用上跟传统的虚拟机的方式相比具有如下显著优势：

+ Docker容器启动很快，启动和停止可以实现秒级，相比传统的虚拟机方式（分钟级）要快速很多。

+ Docker容器对系统资源需求很少，一台主机上可以同时运行数千个Docker容器。

+ Docker通过类似git设计理念的操作来方便用户获取、分发和更新应用镜像，存储复用，增量更新。

+ Docker通过Dockerfile支持灵活的自动化创建和部署机制，可以提高工作效率，并标准化流程。

| **特性** |      **容器**      |    **虚拟机**    |
| :------: | :----------------: | :--------------: |
| 启动速度 |        秒级        |      分钟级      |
|   性能   |      接近原生      |       较好       |
|   内存   |        MB级        |       GB级       |
| 硬盘适应 |        MB级        |       GB级       |
| 运行密度 | 单台主机支持上千个 | 单台主机支持几个 |
|  隔离性  |      安全隔离      |     完全隔离     |
|   迁移   |        优秀        |       一般       |

## 二、Docker与虚拟化

Docker以及其他容器技术，都属于操作系统虚拟化范畴，操作系统细腻化最大的特点就是不需要额外的supervisor支持。Docker虚拟化方式之所以有众多优势，跟操作系统虚拟化技术自身的设计和实现分不开。

![image-20210915120033822](https://i.loli.net/2021/09/15/dPGvBtUb6Y2jRXp.png)

## 三、Docker概念和使用

Docker中有三个核心概念：镜像、容器和仓库。因此，准确把握这三大概念对于掌握Docker技术尤为重要。

+ 镜像（Image）

  Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。

+ 容器（Container）

  镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

+ 仓库（Repository）

+ 用来保存镜像的仓库。当我们构建好自己的镜像之后，需要存放在仓库中，当我们需要启动一个镜像时，可  以在仓库中下载下来。

### 1. 安装

Docker引擎是使用Docker容器的核心组件，可以在主流的操作系统和云平台上使用，包括Linux操作系统、MacOSS和Windows系统上。用户可以访问Docker官网（https://www.docker.com/get-started）去获取Docker安装包

#### 1.1 第一个docker实例

```bash
root@alvin-test-os:~# docker run -d --rm --name nginx -p 80:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
bb79b6b2107f: Pull complete 
5a9f1c0027a7: Downloading [==============>               ]  7.457MB/26.49MB
5a9f1c0027a7: Downloading [======================>      ]  12.17MB/26.49MB
166a2418f7e8: Download complete 
1966ea362d23: Download complete 
```

### 2. 使用Docker镜像

docker镜像是docker中三大概念之一，其主要作用是作为启动容器的模板。

#### 2.1 获取镜像

镜像是运行容器的模板，官方Docker Hub 仓库已经提供了许多镜像共开发者使用。如果我们需要获取某个镜像则可以去docker仓库下载所需的镜像。

下载镜像的格式：docker pull [仓库的URL]/[名称空间]/[仓库的名称]:[版本号]

```bash
root@alvin-test-os ~]# docker pull nginx:1.17
1.17: Pulling from library/nginx
afb6ec6fdc1c: Pull complete
b90c53a0b692: Pull complete
11fa52a0fdc0: Pull complete
Digest: sha256:6fff55753e3b34e36e24e37039ee9eae1fe38a6420d8ae16ef37c92d1eb26699
Status: Downloaded newer image for nginx:1.17
docker.io/library/nginx:1.17
```

#### 2.2 查看镜像信息

镜像主要包括镜像文件、镜像tag以及镜像详细信息等等

##### 2.2.1 镜像列表

使用docker images 或 docker images ls命令可以列举本地主机上已有镜像的基本信息。

![image-20210915121829565](https://i.loli.net/2021/09/15/hNxPRw2WaVnbOkt.png)

列举信息中，可以看到几个字段信息：

+ 镜像来源：来自哪个仓库. docker.io/halohub/halo

+ 镜像标签：latest

+ 镜像ID：例如 caebc1d425f9

+ 镜像创建时间：例如：7 weeks ago

+ 镜像大小：326 MB

其中镜像的ID信息十分重要，它唯一标识了镜像。在使用镜像ID的时候，一般可以使用该ID的前若干个字符组成的可区分串来替代完整的ID。

TAG信息用于标记来自同一个仓库的不同镜像。TAG在同一个仓库中是唯一的。

镜像大小信息只是表示了该镜像的逻辑体积大小，实际上由于相同的镜像层本地只会存储一份，物理上占用的存储空间会小于各镜像逻辑体积之和。

image子命令主要支持如下选项:

+ -a 列出所有(包括临时文件)镜像文件

+ ![image-20210915124049820](https://i.loli.net/2021/09/15/SKmfu53sPY4n9qN.png)

+ -digests=true|false: 列出镜像的数字摘要值

  ![image-20210915124123621](https://i.loli.net/2021/09/15/kFUnchDVPAr95N4.png)

+ -q: 仅显示ID信息

  ![image-20210915124222005](https://i.loli.net/2021/09/15/93gAUMEwJobhqXc.png)

##### 2.2.2 为镜像添加tag

为了方便后续工作使用特定的镜像,还可与你docker tag命令为本地的镜像添加标签

```bash
docker tag halo:latest myhalo:latest
```

##### 2.2.3 使用inspect命令查看详细信息

使用`docker inspect`命令获取镜像的详细信息, 包括PID、作者、架构等等

![image-20210915125327938](https://i.loli.net/2021/09/15/hqLtoJgf9iQalXv.png)

2.2.4 使用history命令查看镜像历史

`docker history [id]`

![image-20210915125730417](https://i.loli.net/2021/09/15/D2x1jetbMUOfCYo.png)

> 注意：有些构建信息过长，可以使用--no-trunc选项来输出完整信息。

##### 2.2.5 搜索镜像

`docker search [option] name`

+ -f: 过滤输出内容
+ -- limit: 限制输出结果
+ --no-trunc: 不截断输出内容

**输出参数释义**

+ NAME: 镜像仓库源的名称

+ DESCRIPTION: 镜像的描述

+ OFFICIAL: 是否 docker 官方发布

+ stars: 类似 Github 里面的 star，表示点赞、喜欢的意思。

+ AUTOMATED: 自动构建。

![image-20210915130725482](https://i.loli.net/2021/09/15/yqdUWhL5gQPrKOe.png)

##### 2.2.6 删除和清理镜像

在Docker中,删除镜像主要使用rmi子命令, 清理镜像主要使用prune子命令

###### 2.2.6.1 使用标签删除镜像

`docker rmi halo ` or  `docker halo rm`

+ -f 强制删除

###### 2.2.6.2 清理镜像

使用一段时间之后，docker会产生很多临时文件，以及一些没有被使用的镜像， 我们可以通过`docker image prune`命令来进行清理。

+ -a 删除所有无用的镜像,不光是临时镜像

##### 2.2.7 构建镜像

构建镜像一般有三种情况，基于容器导入、基于本地模板导入、基于Dockerfile创建，本章主讲基于容器保存镜像和本地保存镜像导入。

###### 2.2.7.1 基于容器保存

`docker [container id] commit`

主要参数:

| -a   | 作者信息             |
| ---- | -------------------- |
| -m   | 提交信息             |
| -p   | 提交时，暂停容器运行 |

启动一个容器、修改并保存

```bash
root@alvin-test-os ~]# docker run -d -it centos /bin/bash
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
3c72a8ed6814: Pull complete
Digest: sha256:76d24f3ba3317fa945743bb3746fbaf3a0b752f10b10376960de01da70685fbd
Status: Downloaded newer image for centos:latest
820a17fe3935938b0d4208797f165db22e205e2a15d6dd053b4aeedbcba6cb12
[root@alvin-test-os ~]# docker ps
CONTAINER ID        IMAGE                                                                            COMMAND                  CREATED              STATUS              PORTS                    NAMES
820a17fe3935        centos                                                                           "/bin/bash"              About a minute ago   Up About a minute                            happy_kare
[root@alvin-test-os ~]# docker exec 820a17fe3935 touch test
[root@alvin-test-os ~]# docker  commit -m "Add a file" -a "Alvin" 820a17fe3935 centos:v1
sha256:407b93005a8f27cd7eba5a10077994cf27c7a978e542fcf5b85a9caed0003bc9
[root@alvin-test-os ~]# docker images
REPOSITORY                                        TAG                              IMAGE ID            CREATED             SIZE
centos                                            v1                               407b93005a8f        4 seconds ago       215MB
```

###### 2.2.7.2 基于本地模板导入

用户也可以将保存的镜像传到另一台电脑上,另一台计算机就可以导入

```bash
cat ubuntu-18.04-x86_64-minimal.tar.gz | docker import - ubuntu:18.04
```

##### 2.2.8 保存镜像

有时我们需要将一台电脑上的镜像复制到另一台电脑上使用，除了可以借助仓库外，还可以直接将镜像保存成一个文件，再拷贝到另一台电脑上导入使用。

对于镜像的导出和导入，Docker 提供了两种方案，下面分别进行介绍。

###### 2.2.8.1 使用export和import

export和import的针对点是容器,将本机的容器导出为镜像包

+ 使用export 保存容器为镜像

  ![](https://i.loli.net/2021/09/15/ue7pFU2Gr91NWmA.png)

+ 使用import导入包为镜像

  ```bash
  [root@linux ~]# docker import halo.tar test/halo:v1
  sha256:02107323de1b074c5d2034b01eff855fec5922b45776c2721882d100ba6dd15b
  [root@linux docs]# docker images | grep test
  test/halo                                                        v1                         02107323de1b        22 seconds ago      131MB
  
  ```

###### 2.2.8.2 使用save和load

save和load的针对的点事镜像,将本机的镜像导入,导出为镜像包

+ 使用save保存镜像

  ```bash
  [root@linux ~]# docker save 6858809bf669 > halo.tar
  [root@linux ~]# ll | grep halo
  -rw-r--r-- 1 root root   1458176 9月  17 21:01 halo.tar
  ```

+ 使用save保存多个镜像

  ```bash
  [root@linux ~]# docker save -o test.tar busybox nginx:1.18.0
  [root@linux ~]# docker load < test.tar
  Loaded image: busybox:latest
  Loaded image: nginx:1.18.0
  ```

+ 使用load导入镜像

  ```bash
  [root@linux ~]# docker load < busybox.tar[root@linux ~]# docker load -i busybox.tarLoaded image ID: sha256:6858809bf669cc5da7cb6af83d0fae838284d12e1be0182f92f6bd96559873e3[root@instance-gvpb80ao docs]# docker images | grep 685880busybox                                                           latest                     6858809bf669        2 weeks ago         1.23MB
  ```

**两种方案的区别**

+ export导出的镜像文件体积小于save保存的镜像

+ docker import 可以为镜像指定新名称,docker load不能对载入的镜像重命名

+ docker export不支持将多个镜像打包到一个tar文件中买单时docker save支持

+ export导出(import导入)是根据容器拿到的镜像,在导入的时候会丢失镜像多有的历史记录和元数据信息(即今保存容器当时的快照状态),所以无法进行回滚操作

  save保存(load加载)的镜像, 没有丢失镜像的历史,可以回滚到之前的层(layer)

+ 同时他们之间的应用场景也是不一样的

  + docker export的应用场景:

    主要是用来制作基础镜像,比如我们从一个ubuntu镜像启动一个容器,然后安装一些软件和进行一些设置后,使用docker export保存一个基础镜像. 然后把这个镜像分发给其他人使用,比如作为基础的开发环境.

  + docker save的应用场景:

    如果我们的应用shiyongdocker-compose.yml编排的多个镜像组合,但是我们要部署的客户端服务器不能连接外网. 这是后悔我们可以使用docker save将用到的镜像打个包,然后拷贝到客户端服务器上使用,docker load载入

### 3. 使用Docker容器

#### 3.1 创建容器

在Docker中,真正对外提供服务的还是容器, 我们需要了解如何创建容器

格式: `docker run [option] image [cmd]`

```bash
[root@Linux ~]# docker run -d --name nginx -p 80:80 nginxUnable to find image 'nginx:latest' locallylatest: Pulling from library/nginx852e50cd189d: Pull completea29b129f4109: Pull completeb3ddf1fa5595: Pull completec5df295936d3: Pull complete232bf38931fc: Pull completeDigest: sha256:c3a1592d2b6d275bef4087573355827b200b00ffc2d9849890a4f3aa2128c4aeStatus: Downloaded newer image for nginx:latest6381d29d6e0ec3f6b01cf1aabb58b799ee88acf1a722e251807c9cb44e73a3e0
```

##### 3.1.1 容器启动参数

| -d        | 以守护进程方式运行       |
| --------- | ------------------------ |
| -p        | 指定映射端口             |
| -P        | 随机映射端口             |
| -i        | 保持标准输入打开         |
| -t        | 分配一个伪终端           |
| -v        | 设置挂载文件到主机上     |
| --rm      | 当容器关闭时自动删除     |
| --name    | 为启动的容器设置一个名字 |
| --network | 指定使用哪个网络         |
| -e        | 设置容器中的环境变量     |
| --link    | 链接到另一个容器         |
| -h        | 指定容器内的主机名       |

#### 3.2 停止容器

Docker终止容器是首先向容器发送SIGTERM信号,等到一段时间超时后(默认10s),zaifasongSIGKILL信号来终止容器

![image-20210917170901679](https://i.loli.net/2021/09/17/GrY2Ao6Fm3O7cWw.png)

这时候我们可以看到shadowbox已经被我们终止了, 还可以使用start命令开启这个容器

![image-20210917171015882](https://i.loli.net/2021/09/17/Ua6JYk3VtNP5vzC.png)

#### 3.3 进入容器

在使用容器的过程中, 我们难免需要进入容器进行排查问题. 下面有如下方式:

+ attach

  attach是最早docker官方推出的进入容器的命令,不过使用这个命令有一个问题,当多个窗口同时使用这个命令的时候,所有的窗口都会同步显示. 如果有一个窗口阻塞了,那么其他窗口也无法在进行操作,当所有窗口退出时,容器结束

  ```bash
  [root@Linux ~]# docker attach nginx127.0.0.1 - "GET / HTTP/1.1" 308 171 "-" "curl/7.59.0" 0.000 - .
  ```

+ exec

  继attach之后,exec是官方推出的一个新的命令进入容器的方式,这个命令相当于在容器中执行一个命令

  ```bash
  [root@Linux ~]# docker exec -it nginx /bin/bashnginx [ / ]$nginx [ / ]$
  ```

+ nsenter

  需要配合docker inspect来使用(早期的时候没有exec命令,企业中最常用的方式之一),Docker是使用golang语言开发的,所以他也支持go语言的模板语法

  ```bash
  [root@Linux ~]# nsenter --target $( docker inspect -f {{.State.Pid}} nginxv1 ) --mount --uts --ipc --net --pidmesg: ttyname failed: No such deviceroot@6f99ae8757f7:/#
  ```

+ ssh

  在生产环境当中排除了使用docker attach命令进入容器之后,相信大家第一个想到的就是ssh. 在镜像或容器中安装ssh server, 这样就能保证多人进入容器并且相互之间不受干扰了,相信大家在之前的生产环境中(没有使用docker的情况)也是这样做的. 但是使用了docker容器之后不建议使用ssh进入docker容器中.

  > **总结:**
  >
  > 进入docker container中一般情况下有4种范式,最常用的方式是exec和nsenter这两种.
  >
  > **nsenter和exec之间的区别**
  >
  > + exec是docker自带的命令,nsenter是Linux提供的命令
  > + exec相当于在容器中执行一个命令,而nsenter仅仅是进入某一个容器而已

#### 3.4 删除容器

可以使用docker rm来删除容器

格式: `docker rm contaier`

+ 强制删除 -f

  ```bash
  docker rm -f nginx
  ```

#### 3.5 导入导出容器

某些时候需要将容器从一个系统迁移到另外一个系统,此时可以使用docker的导入和导出功能,这就是docker自身提供的一个重要特性

```bash
docker export [id] > name.tardocker import name.tar test/name:v1
```

实际上,即可以使用docker load命令来导入镜像文件到本地镜像库,也可以使用docker [container] import 命令, 这两者的区别就在于是否丢弃历史记录和元数据信息

#### 3.6 查看容器

格式:

`docker container inspact [options] CONTAINER [CONTAINER ...]`子命令

```bash
[root@Linux~]# docker inspect k8s[    {        "Id": "726b695a337c63b2ba617ed282171dff82a3d51cc0bdc43194ce63beaf656c38",        "Created": "2020-11-16T04:05:53.271281122Z",        "Path": "docker-entrypoint.sh",        "Args": [            "./start"            ...            ],    }]
```

#### 3.7 容器命令详解

+ 复制命令

  复制命令类似于Linux系统中的scp命令, 是将宿主主机上的内容上传到容器中给你,也可能是将容器中的文件下载到宿主主机当中国n

  ```bash
  # 将容器中的内容复制到宿主主机[root@Linux ~]# docker cp 762b695a337c:/opt/start .[root@Linux ~]# ls | grep startStart# 将宿主主机中的文件复制到容器中[root@Linux ~]# docker cp start 762b695a337c:/root[root@Linux ~]# docker exec 762b695a337c ls /root
  ```