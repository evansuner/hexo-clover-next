---
title: Docker3-镜像制作Dockerfile
date: 2022-01-06 00:52:46.153
updated: 2022-01-06 00:52:46.153
url: /?p=104
categories: 
- 服务器
tags: 
- docker
---

> 当我们从docker镜像仓库中下载的镜像已经不能满足我们的需求的时候，我们可以通过以下两种方式对镜像进行修改。
> + 1. 从已经创建的容器中更新镜像，并且提交这个镜像
> + 2. 使用Dockerfile指令来创建一个新的镜像
> 好处：
> 1. 对开发人员来说，可以为开发团队提供一个完全一致的开发环境
> 2. 对于测试人员来说，可以直接拿开发时所构建的镜像测试
> 3. 对于运维人员来说，在部署的是洪湖可以快速实现部署和移植

## Dockerfile定制镜像
镜像的定制实际上就是定制每一层所添加的配置、文件。**如果我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像**，那么之前提及的无法重复的问题、镜像构建透明性的问题、体积的问题都会随之解决。这个脚本就是**Dockerfile**
Dockerfile是一个文本文件，期中包含了一条条指令（instructions），每一条指令构建一层，因此每一条指令的内容就是描述该层应当如何构建。

## Dockerfile常用命令
+ **FROM** --指定基础镜像
    > 基础镜像不存在会在Dockerhub上去拉取（一般会出现在文件的第一个指令）
    > 使用格式：`FROM <镜像>:[tag]`
    > `FROM <镜像>@digest[校验码]当前主机没有此镜像的时候，会自动去官网hub下载`
+ **MAINTAINER** --提供Dockerfile制作者的信息
    > 逐渐废弃 使用**LABLE**替代MAINTAINER 具体使用 LABLE maintainer="作者信息"
    > ```bash
    > LABLE maintainer="xxx@xx.com"
    > LABLE "com.example.vendor"="ACME Incorporated"
    > LABLE version="1.0"
    > LABLE description="This text illustrates taht lable-values can span multiple lines"
    > ```
+ **ENV** --设置环境变量
    > 用于为docker容器设置环境变量 ENV设置的环境变量，可以使用docker inspect命令查看。同事还可以使用docker run --env=来修改环境变量
    > 具体用法：
    > ```bash
    > ENV JAVA_HOME /usr/local/jdk
    > ENV JRE_HOME $JAVA_HOME/jre
    > ENV CLASSPATH $JAVA_HOME/lib/:$JRE_HOME/lib
    > ENV PATH $PATH:$JAVA_HOME/bin
    > ```
+ **USER** --切换运行属主身份
    > Docker默认使用root，但是若不需要，建议切换使用者身份，毕竟root权限太大，有安全风险
+ **WORKDIR** --切换工作目录
    > Docker 默认的工作目录是/，只有 RUN 能执行 cd 命令切换目录，而且还只作用在当下下的 RUN，也就是说每一 个 RUN 都是独立进行的。
如果想让其他指令在指定的目录下执行，就得靠 WORKDIR。WORKDIR 动作的目录改变是持久的，不用每个指 令前都使用一次 WORKDIR。
    > ```bash
    > WORKDIR /usr/local/tomcat
    > ```
+ **VOLUME** --创建可以从本地主机或者其他容器的挂载点，一般用来存放数据库和需要保持的数据等
    > 数据卷
    > 只能定义docker管理的数据卷：VOLUME /data/mysql运行的时候会随机在宿主机的目录下生成一个卷目录
+ **COPY** --把宿主机中的文件复制到镜像中去
    > 文件要在Dockerfile工作目录src源文件
    > --支持通配符
    > --通常相对路径 dest 目标路径
    > --通常绝对路径
+ **ADD** --类似COPY命令
    > ADD 将文件从路径 复制添加到容器内部路径 。
    > 必须是想对于源文件夹的一个文件或目录，也可以是一个远程的url。
    > 是目标容器中的绝对路径。 所有的新文件和文件夹都会创建UID 和 GID。事实上如果 是一个远程文件URL，那么目标文件的权限将会是600。
+ **EXPOSE** --为容器打开指定的要监听的端口以实现与外部通信
    > 使用格式: `EXPOSE 80/tcp 23/udp`
    > 不加协议默认为tcp
    > 使用-P选项可以暴露这里指定的端口! 但是宿主的关联至这个端口的端口是随机的!
+ **RUN**
    > RUN 指令是用来执行命令行命令的。由于命令行的强大能力，RUN 指令在定制镜像时是最常用的指令之一。
    > 其格式 有两种:   
    > • shell 格式:RUN <命令>，就像直接在命令行中输入的命令一样。刚才写的 Dockerfile 中的 RUN 指令就是 这种格式。   
    > • exec 格式:RUN ["可执行文件", "参数1", "参数2"]，这更像是函数调用中的格式。
    > 使用格式：`RUN RUN ["","",""]`

## <font style="color:red;font-weight">注意：</font>
>   `RUN`就像shell脚本一样可以执行命令，那么我们是否就可以像shell脚本一样把每一个命令对应一个RUN呢？
> ```bash
> RUN apt-get update
> RUN apt-get install -y gcc libc6-dev make
> RUN wget https://download.redis.io/releases/redis-4.0.1.tar.gz
> RUN tar xzf redis-4.0.1.tar.gz
> # 注意下面这个是用方式是错误的
> RUN cd redis-4.0.1
> # 应该使用
> WORKDIR redis-4.0.1
> ```
> Dockerfile 中每一个指令都会建立一层，RUN 也不例外。每一个 RUN 的行为，和刚才我们手工建立镜像的过程一样: 新建立一层，在其上执行这些命令，执行结束后，commit 这一层的修改，构成新的镜像。 而上面的这种写法，创建 了 多 层镜像。这是完全没有意义的，而且很多运行时不需要的东西，都被装进了镜像里，比如编译环境、更新的软 件包等等。结果就是产生非常臃肿、非常多层的镜像，不仅仅增加了构建部署的时间，也很容易出错。 这是很多初 学 Docker 的人常犯的一个错误。
> 

> `Union FS`是有最大层数限制的，比如`AUFS`，曾经是最大不得超过42层，现在是不得超过127层。上面的Dockerfile应该写成这样：   
> ```bash
> FROM centos
> RUN yum update \
> && yum install -y gcc libc6-dev make \
> && wget http://http://download.redis.io/releases/redis-4.0.1.tar.gz \
> && tar xzf redis-4.0.1.tar.gz \
> && cd redis-4.0.1
> ```
> 首先，之前所有的命令只有一个目的，就是编译、安装 redis 可执行文件。因此没有必要建立很多层，这只是一层的 事情。因此，这里没有使用很多个 RUN 对一一对应不同的命令，而是仅仅使用一个 RUN 指令，并使用 && 将各个所 需命令串联起来。将之前的 7 层，简化为了 1 层。在撰写 Dockerfile 的时候，要经常提醒自己，这并不是在写 Shell 脚本，而是在定义每一层该如何构建。 并且，这里为了格式化还进行了换行。Dockerfile 支持 Shell 类的行尾添加 \ 的 命令换行方式，以及行首 # 进行注释的格式。良好的格式，比如换行、缩进、注释等，会让维护、排障更为容易，这 是一个比较好的习惯。
> 

## 案例1
> 需求：创建一个镜像，基于tomcat 里面要有一个index.html， 并写入Hello Dockerfile
```bash
# 创建目录 /usr/local/docker/demo1
mkdir -p /usr/local/docker/demo1
cd /usr/local/docker/demo1
# 创建Dockerfile文件并编辑
vim Dockerfile
 
FROM tomcat
RUN mkdir -p /usr/local/tomcat/webapps/ROOT/
ENV ROOT_PATH /usr/local/tomcat/webapps/ROOT/
RUN echo 'Hello Dockerfile'>$ROOT_PATH/index.html
WORKDIR /usr/local/tomcat/webapps/ 

# 使用docker命令构建demo1镜像
docker build -t demo1 .
 
Sending build context to Docker daemon  2.048kB
Step 1/5 : FROM tomcat
---> fb5657adc892
Step 2/5 : RUN mkdir -p /usr/local/tomcat/webapps/ROOT/
---> Running in 719cfc96d9bd
Removing intermediate container 719cfc96d9bd
---> da7e1b8c9cc1
Step 3/5 : ENV ROOT_PATH /usr/local/tomcat/webapps/ROOT/
---> Running in 0f1df4671ccc
Removing intermediate container 0f1df4671ccc
---> 382b57ca243a
Step 4/5 : RUN echo 'Hello Dockerfile'>$ROOT_PATH/index.html
---> Running in 4a0e22281b08
Removing intermediate container 4a0e22281b08
---> c3fb15914ae5
Step 5/5 : WORKDIR /usr/local/tomcat/webapps/
---> Running in a4d3483f03d6
Removing intermediate container a4d3483f03d6
---> 55a361b70247
Successfully built 55a361b70247
Successfully tagged demo1:latest
# 启动镜像demo1
docker run -itd --name demo1 -p 8083:8080 demo1
6954ce246808f596213f8b94751bf6336d1e19c6a41ade7fde06f071ed62d841
# 浏览器输入ip:8083 访问正常
```

## 案例2
> 要求：基于tomcat从外部复制一个文件，并复制到容器中能访问
```bash
cd /usr/local/docker/
mkdir demo2 && cd demo2
# 创建Dockerfile
vim Dockerfile

FROM tomcat
ENV WEBAPPS_PATH /usr/local/tomcat/webapps/
RUN mkdir -p $WEBAPPS_PATH/ROOT/
WORKDIR $WEBAPPS_PATH
COPY 1.png $WEBAPPS_PATH/ROOT/
RUN echo 'Hello Dockerfile'>$WEBAPPS_PATH/ROOT/index.html

# 构建demo2
docker build -t demo2 .
...

# 启动
docker run -itd --name demo2 -p 8084:8080 demo2
...
```
## <font style="color:red">注意：</font>
> **虚悬镜像**
由于对同一个Dockerfile进行修改后使用相同命令进行第二次构建，当前构建的镜像名称以及标签会替代之前构建的镜像，而之前构建的镜像其名称和标签都会变成`<none>`，这个镜像就被称为虚悬镜像，示例代码如下：   
当前有如下镜像demo2：   

![16413959765186](https://mweb-cdn.reidosann.top/mweb/16413959765186-20220105-16413959765269.jpg)

使用相同命令再次构建修改后的Dockerfile：
```bash
➜  demo2 docker build -t demo2 .
Sending build context to Docker daemon  221.2kB
Step 1/6 : FROM tomcat
 ---> fb5657adc892
Step 2/6 : ENV WEBAPPS_PATH /usr/local/tomcat/webapps/
 ---> Using cache
 ---> 0bd8e93aecf3
Step 3/6 : RUN mkdir -p $WEBAPPS_PATH/ROOT/
 ---> Using cache
 ---> 7b27e4d203f8
Step 4/6 : WORKDIR $WEBAPPS_PATH
 ---> Using cache
 ---> 9e5a80151a10
Step 5/6 : COPY 1.png $WEBAPPS_PATH/ROOT/
 ---> Using cache
 ---> 9dd466aadf85
Step 6/6 : RUN echo 'Hello Dockerfile'>$WEBAPPS_PATH/ROOT/index.html
 ---> Using cache
 ---> 6bd403bf9552
Successfully built 6bd403bf9552
Successfully tagged demo2:latest
```

![16413965233897](https://mweb-cdn.reidosann.top/mweb/16413965233897-20220105-16413965233909.jpg)
删除虚悬镜像：
```bash
➜  demo2 docker image prune
...
Total reclaimed space: 0B
# 我们发现并没有删除掉虚悬镜像，因为这个镜像有正在运行的容器
# 我们需要先使用虚悬镜像的镜像ID找到对应的容器ID，并使用容器ID停止删除该容器
➜  demo2 docker stop 02ffcde8beb5
➜  demo2 docker rm 02ffcde8beb5
➜  demo2 docker image prune
...
Total reclaimed space: 17B
```