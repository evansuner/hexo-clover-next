---
title: Docker5-Docker Compose
date: 2022-01-07 21:24:11.019
updated: 2022-01-08 20:19:39.149
url: /?p=107
categories: 
- 服务器
tags: 
- docker
---

> 使用Docker Compose可以轻松、高效的管理容器，他是一个用于定义和运行多容器Docker的应用程序工具。Docker官方编排(Orchestration)项目之一，负责快速的部署分布式应用。

## 安装
```bash
# 将安装包直接下载到本地（版本自行指定）
# 国内访问github速度太慢，建议手动下载后使用sftp上传到服务器
➜  ~ wget https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-linux-x86_64
# 移动到/usr/local/bin 重命名并授权
➜  ~ mv ./docker-compose-linux-x86_64 /usr/local/bin/docker-compose
docker-compose
➜  ~ chmod +x /usr/local/bin/docker-compose
# 查看版本
➜  ~ docker-compose version
Docker Compose version v2.2.3
```
## 使用
### 术语
+ 服务(service)：一个应用容器，实际上可以运行多个相同的镜像实例
+ 项目(project)：由一组关联的应用容器组成一个完整业务单元   

一个项目可以由多个服务（容器）关联而成，Compose面向项目进行管理

### Docker-compose创建容器   
通过一个单独的`docker-compose.yml`模板文件(yaml格式)来定义一组相关联的应用容器为一个项目   
**yml格式描述：**   
+ yml文件以缩进代表层级关系
+ 缩进不允许使用tab，只能使用空格
+ 空格个数不重要，只要相同层级的元素左对齐即可（建议2个）
+ 大小写敏感
+ 数据格式为，name:(空格)value

```yaml
# key:(空格)value：表示一对键值对（空格不能省略），空格控制层级关系，只要是左对齐的数据表示同一级别:
server:
  port: 8083
  path: /helloboot
  
# 数组(用-表示数组中的一个元素):
animal:
- cat
- dog
```
**示例1**   
使用Docker-compose的方式管理一个Tomcat容器和MySQL
```bash
# 创建文件夹，创建对应的目录并创建文件
mkdir -p /opt/docker-compose/docker_mysql_tomcat
cd /opt/docker-compose/docker_mysql_tomcat
vim docker-compose.yml
```
```yaml
version: '3.1'
services:
  mysql:                                        # 服务的名称
    restart: always                             # 只要docker启动，容器会随着启动
    image: daocloud.io/library/mysql:5.7.6      # 指定镜像路径信息(默认官方镜像地址)
    container_name: mysql-3306                  # 指定容器名称 --name 
    ports:                                      # 指定端口号映射
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: root                 # 指定MYSQL ROOT用户的密码 
      TZ: Asiz/Shanghai                         # 指定时区
    volumes:                                    # 映射mysql的数据目录到宿主机，保存数据 
                                                # 把mysql的配置文件映射到容器的相应目录
      - /opt/docker_mysql_tomcat/mysql/data:/var/lib/mysql 
      - /opt/docker_mysql_tomcat/mysql/conf/mysqld.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf 
tomcat:
  restart: always
  image: daocloud.io/library/tomcat:8.5.15-jre8
  container_name: tomcat-8080
  ports:
    - 8080:8080
  environment:
    TZ: Asiz/Shanghai
  volumes:
    - /opt/docker_mysql_tomcat/tomcat/webapps:/usr/local/tomcat/webapps
    - /opt/docker_mysql_tomcat/tomcat/logs:/usr/local/tomcat/logs
```
```bash
# 启动
docker-compose up -d
# 默认执行的文件名：docker-compose.yml
# 如果不是默认文件名，需要使用以下命令执行
docker-compose -f filename.yml up -d 
```
### docker-compose命令(基于Docker-compose.yml文件)
| 命令 | 解释 |
| :---- | :---- |
| build| 构建或重建服务| 
| help| 命令帮助|
|kill |杀掉容器|
|logs |显示容器的输出内容 |
|port |打印绑定的开放端口 |
|ps |显示容器|
|pull| 拉取服务镜像|
|restart| 重启服务|
|rm |删除停止的容器|
|run |运行一个一次性命令 scale 设置服务的容器数目 start 开启服务|
|stop |停止服务|
|up |创建并启动容器 |
|down | 停止并删除所有镜像 |

### 常用命令示例(基于Docker-compose.yml文件)
| 命令 | 解释 |
| :--- | :--- |
|docker-compose up -d nginx |构建启动nignx容器 |
|docker-compose exec nginx bash| 登录到nginx容器中 |
|docker-compose down |删除所有nginx容器,镜像 |
|docker-compose ps |显示所有容器|
|docker-compose restart nginx |重新启动nginx容器|
|docker-compose run --no-deps --rm php-fpm php -v |在php-fpm中不启动关联容器，并容器执行php -v 执行完成后删除容器|
|docker-compose build nginx |构建镜像 |
|docker-compose build --no-cache nginx |不带缓存的构建|
|docker-compose logs nginx |查看nginx的日志|
|docker-compose logs -f nginx |查看nginx的实时日志|
|docker-compose config -q |验证(docker-compose.yml)文件配置，当配置正确时，不输出任何内容，当文件配 置错误，输出错误信息。|
|docker-compose events --json nginx |以json的形式输出nginx的docker日志 |
|docker-compose pause nginx |暂停nignx容器|
|docker-compose unpause nginx| 恢复ningx容器|
|docker-compose rm nginx |删除容器(删除前必须关闭容器)|
| docker-compose stop nginx |停止nignx容器|
|docker-compose start nginx |启动nignx容器|