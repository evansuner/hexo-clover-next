---
title: 使用Docker搭建Django应用
date: 2022-01-27 08:32:23.872
updated: 2022-01-27 08:32:23.872
url: /?p=207
categories: 
- 服务器
- 后端
tags: 
- django
- docker
---


## django_docker_img:v1   
Django项目v1：使用内置的runserver启动   
项目树形图:
![16430634518434](https://mweb-cdn.reidosann.top/mweb/16430634518434-20220125-16430634518443.jpg)

### 创建django应用
```bash
mkdir mysite && cd mysite
django-admin startproject mysite1
cd mysite1
```
### 修改settings.py文件
```
cd mysite1
vim settings.py

ALLOWED_HOSTS = ['x.x.x.x'] # 服务器ip地址
```
### 创建requirements.txt
```bash
cd ..
pip3 freeze > requirements.txt
```
### 创建 pip.conf
```bash
vim pip.conf

[global]
index-url="http://mirrors.cloud.tencent.com/pypi/simple"
[install]
trusted-host="mirrors.cloud.tencent.com"
```
### 创建Dockerfile
```bash
vim Dockerfile

FROM python:3.9
# 设置Python变量环境
ENV PYTHONRUNBUFFER 1
# 设置pip源
COPY pip.conf /root/.pip/pip.conf
RUN mkdir -p /var/www/html/mysite1
WORKDIR /var/www/html/mysite1

# 将所有代码加入到容器中
ADD . /var/www/html/mysite1

# 安装依赖
RUN pip3 install -r requirements.txt
```
### 创建镜像 `django_docker_img:v1`
```bash
docker build -t django_docker_img:v1 .
```
### 查看镜像
![16430625778275](https://mweb-cdn.reidosann.top/mweb/16430625778275-20220125-16430625778295.jpg)

### 创建容器
```bash
## 服务器安全组需要先将5000端口打开
docker run -itd --name mysite1 -p 5000:8000 django_docker_img:v1
```
### 启动django项目
```bash
docker exec -it mysite1 bash
## 启动项目
python3 manage.py makemigrations
python3 manage.py migrates
python3 manage.py runserver 0.0.0.0:8000
```
接下来就可以在浏览器通过服务器外网ip+端口号访问了 `x.x.x.x:5000`

## django_docker_img:v2
基于v1版本，加入uwsgi配置   
项目树形图：

![16430640121348](https://mweb-cdn.reidosann.top/mweb/16430640121348-20220125-16430640121355.jpg)

### v2的Dockerfile
```bash
FROM python:3.9
ENV PYTHONUNBUFFERED 1
COPY pip.conf /root/.pip/pip.conf
RUN mkdir -p /var/www/html/mysite2
WORKDIR /var/www/html/mysite2
ADD . /var/www/html/mysite2
RUN pip3 install -r requirements.txt
# 消除unix环境下换行\n+回车\r问题
RUN sed -i 's/\r//' ./start.sh
RUN chmod +x ./start.sh
```
### 编写start.sh
```bash
#!/bin/bash
python3 manage.py makemigrations &&
python3 manage.py migrate &&
uwsgi --ini /var/www/html/mysite2/uwsgi.ini
```
### 编写uwsgi.ini
```bash
[uwsgi]
http= 0.0.0.0:8000 # 因为部署在服务器上，本地访问，所以不能使用127.0.0.1
chdir = ../mysite2
module = mysite2.wsgi:application
vacuum=True
master = True
max-requests=5000
buffer-size = 65536
daemonize=./mysite2.log
processes=5
# 请求超时时间 秒
# harakiri=60
# 当一个请求被harakiri杀掉时,输出一条日志
# harakiri-verbose=true
```
### 创建镜像，启动容器，启动服务
```bash
docker build -t django_docker_img:v2
docker run -itd --name mysite2 -p 5000:8000 django_docker_img:v2
sh ./start.sh
```
浏览器访问`x.x.x.x:5000`，此时使用的是uwsgi启动的服务。

## django_docker_img:v3   
使用双容器部署 `Django + uwsgi + Nginx`
基于v2版本加入Nginx配置   
项目树形图：   

![16431134649959](https://mweb-cdn.reidosann.top/mweb/20220125-16431134649968.jpg
![16430745966537](https://mweb-cdn.reidosann.top/mweb/16430745966537-20220125-16430745966545.jpg)

### 修改settings.py
```bash
# nginx采用动静分离，所以必须要在settings中配置MEDIA_ROOT和STATIC_ROOT
STATIC_ROOT = os.path.join(BASE_DIR,'static')
STATIC_URL = "/static/"

MEDIA_ROOT = os.path.join(BASE_DIR,'media')
MEDIA_URL = "/media/"
```
### Django Dockerfile
```bash
FROM python:3.9
ENV PYTHONUNBUFFERED 1
COPY pip.conf /root/.pip/pip.conf
RUN mkdir -p /var/www/html/mysite3
WORKDIR /var/www/html/mysite3
ADD . /var/www/html/mysite3
RUN pip3 install -r requirements.txt
# 消除unix环境下换行\n+回车\r问题
RUN sed -i 's/\r//' ./start.sh
RUN chmod +x ./start.sh
```

### 修改start.sh
```bash
# 收集静态文件
python3 manage.py collectstatic --noinput &&
python3 manage.py makemigrations &&
python3 manage.py migrate &&
uwsgi --ini /var/www/html/mysite3/uwsgi.ini
```

### 修改uwsgi.ini
```bash
[uwsgi]
# 因为需要与nginx之间进行通信，所以将http改为socket
socket= 0.0.0.0:8000 # 因为部署在服务器上，本地访问，所以不能使用127.0.0.1
chdir = ../mysite3
module = mysite3.wsgi:application
vacuum=True
master = True
max-requests=5000
buffer-size = 65536
daemonize=./mysite3.log
processes=5
```
### 创建镜像，启动容器
```bash
docker build -t django_docker_img:v3 .
docker run -itd --name mysite3 -p 5000:8000 django_docker_img:v3
```

### 编写nginx镜像 Dockerfile
```bash
FROM nginx:latest

RUN rm /etc/nginx/conf.d/default.conf \
&& mkdir -p /usr/local/share/nginx/html/static \
&& mkdir -p /usr/local/share/nginx/html/media \
&& mkdir -p /usr/local/share/nginx/html/ssl

ADD ./nginx.conf /etc/nginx/conf.d

# 关闭守护模式
CMD ["nginx", "-g", "daemon off;"]
```

### 编写nginx.conf
```bash
upstream django {
    ip_hash;
    server 172.18.0.2:8000; # 这里ip使用 docker inspect mysite3 | grep "IPAddress" 查看
}
server {
    listen 80;
    server_name localhost;
    location /static {
        alias /usr/share/nginx/html/static;
    }
    location /media {
        alias /usr/local/share/nginx/html/media;
    }
    location / {
        include /etc/nginx/uwsgi_params;
        uwsgi_pass django;
        uwsgi_read_timeout 600;
        uwsgi_connect_timeout 600;
        uwsgi_send_timeout 600;
    }
}
    access_log /var/log/nginx/access.log main;
    error_log /var/log/nginx/error.log warn;
```

### 创建nginx镜像并启动
```bash
docker build -t mynginx:v1 .
docker run -itd --name mysite3-nginx \
-v ~/mysite3path/static:/usr/local/share/nginx/html/static \
-v ~/mysite3path/media:/usr/local/share/nginx/html/media \
-v ~/mysite3path/ssl:/usr/local/share/nginx/html/ssl \
-p 8000:80 \
mynginx:v1
```
### 进入django容器，启动uwsgi服务
```bash
docker exec -it mysite3 bash start.sh
```
接下来访问`x.x.x.x:5000`服务就是经过uwsgi启动，nginx转发的服务了。

## django_docker_img:v4
利用`docker-compose`部署`Django+uwsgi+nginx+mysql+redis`
+ Django + uwsgi容器：核心应用程序，处理动态请求
+ MySQL容器：数据库服务器
+ Redis容器：缓存服务
+ Nginx容器：反向代理并处理静态资源请求   

依赖关系：   
Django+uwsgi依赖redis容器和mysql容器，nginx容器依赖django+uwsgi容器   

图示关系：
![container_relies](https://mweb-cdn.reidosann.top/mweb/20220127-16432403750528.jpg)
项目树形图：
![16430745966537](https://mweb-cdn.reidosann.top/mweb/16430745966537-20220125-16430745966545.jpg)

### 编写docker-compose.yml
```bash
 version: "3"
 
 volumes: # 自定义数据卷，位于宿主机/var/lib/docker/volumes内
   mysite4_db_vol: # 定义数据卷同步容器内mysql数据
   mysite4_redis_vol: # 定义数据卷同步redis容器内数据
   mysite4_media_vol: # 定义数据卷同步media文件夹数据
 
 services:
   redis:
     image: redis:5
     command: redis-server /etc/redis/redis.conf # 容器启动后启动redis服务器
     volumes:
       - mysite4_redis_vol:/data # 通过挂载给redis数据备份
       - ./compose/redis/redis.conf:/etc/redis/redis.conf # 挂载redis配置文件
     ports:
       - "6379:6379"
     restart: always # always表容器运行发生错误时一直重启
 
   db:
     image: mysql:5.7
     environment:
       - MYSQL_ROOT_PASSWORD=123456 # 数据库密码
       - MYSQL_DATABASE=mysite4 # 数据库名称
       - MYSQL_USER=dbuser # 数据库用户名
       - MYSQL_PASSWORD=password # 用户密码
 
     volumes:
       - mysite4_db_vol:/var/lib/mysql:rw # 挂载数据库数据, 可读可写
       - ./compose/mysql/conf/my.cnf:/etc/mysql/my.cnf # 挂载配置文件
       - ./compose/mysql/init:/docker-entrypoint-initdb.d/ # 挂载数据初始化sql脚本
     ports:
       - "3306:3306" # 与配置文件保持一致
     restart: always
 
   web:
     build: ./mysite4 # 使用mysite4目录下的Dockerfile
     expose:
       - "8000"
     volumes:
       - ./mysite4:/var/www/html/mysite4 # 挂载项目代码
       - mysite4_media_vol:/var/www/html/mysite4/media # 以数据卷挂载容器内用户上传媒体文件
       - ./compose/uwsgi:/tmp # 挂载uwsgi日志
     links:
       - db
       - redis
     depends_on: # 依赖关系
       - db
       - redis
     environment:
       - DEBUG=False
     restart: always
     tty: true
     stdin_open: true
 
   nginx:
     build: ./compose/nginx
     ports:
       - "80:80"
       - "443:443"
     expose:
       - "80"
     volumes:
       - ./mysite4/static:/usr/share/nginx/html/static # 挂载静态文件
       - ./compose/nginx/ssl:/usr/share/nginx/ssl # 挂载ssl证书目录
       - ./compose/nginx/log:/var/log/nginx # 挂载日志
       - mysite4_media_vol:/usr/share/nginx/html/media # 挂载用户上传媒体文件
     links:
       - web
     depends_on:
       - web
     restart: always
```
### 编写 django+uwsgi镜像Dockerfile
```bash
FROM python:3.9
ENV PYTHONUNBUFFERED 1
COPY pip.conf /root/.pip/pip.conf
RUN mkdir -p /var/www/html/mysite4
WORKDIR /var/www/html/mysite4
ADD . /var/www/html/mysite4
RUN pip3 install --upgrade pip
RUN pip3 install -r requirements.txt

# 消除unix环境下换行\n+回车\r问题
RUN sed -i 's/\r//' ./start.sh
RUN chmod +x ./start.sh
```
### requirements.txt内容
```bash
Django==3.0.5
django-redis==5.2.0
PyMySQL==1.0.2
redis==4.1.1
uWSGI==2.0.20
```

### 编写start.sh脚本
```bash
#!/bin/bash
python3 manage.py collectstatic --noinput &&
python3 manage.py makemigrations &&
python3 manage.py migrate &&
uwsgi --ini /var/www/html/mysite4/uwsgi.ini
```

### 编写uwsgi.ini配置文件
```bash
[uwsgi]

socket= 0.0.0.0:8000
chdir = ../mysite4
module = mysite4.wsgi:application
vacuum=True
master = True
max-requests=5000
buffer-size = 65536
daemonize=./mysite4.log
processes=5
```
### 编写nginx镜像文件
```bash
FROM nginx:latest

RUN rm /etc/nginx/conf.d/default.conf \
&& mkdir -p /usr/local/share/nginx/html/static \
&& mkdir -p /usr/local/share/nginx/html/media \
&& mkdir -p /usr/local/share/nginx/html/ssl

ADD ./nginx.conf /etc/nginx/conf.d

CMD ["nginx", "-g", "daemon off;"]
```
### 编写nginx配置文件
```bash
upstream django {
        ip_hash;
        server web:8000;
}
server {
    listen 80;
    server_name localhost;
    location /static {
        alias /usr/local/share/nginx/html/static;
    }
    location /media {
        alias /usr/local/share/nginx/html/media;
    }
    location / {
        include /etc/nginx/uwsgi_params;
        uwsgi_pass django;
        uwsgi_read_timeout 600;
        uwsgi_connect_timeout 600;
        uwsgi_send_timeout 600;

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_set_header X-Real-IP  $remote_addr;
    }
}
    access_log /var/log/nginx/access.log main;
    error_log  /var/log/nginx/error.log warn;
    
    server_token off;
```

### 编写MySQL容器配置文件
```bash
 # compose/mysql/conf/my.cnf
 [mysqld]
 user=mysql
 default-storage-engine=INNODB
 character-set-server=utf8
 
 port            = 3306 # 端口与docker-compose里映射端口保持一致
 #bind-address= localhost #一定要注释掉，mysql所在容器和django所在容器不同IP
 
 basedir         = /usr
 datadir         = /var/lib/mysql
 tmpdir          = /tmp
 pid-file        = /var/run/mysqld/mysqld.pid
 socket          = /var/run/mysqld/mysqld.sock
 skip-name-resolve  # 这个参数是禁止域名解析的，远程访问推荐开启skip_name_resolve。
 
 [client]
 port = 3306
 default-character-set=utf8
 
 [mysql]
 no-auto-rehash
 default-character-set=utf8
```

### 编写MySQL启动时执行的脚本
```bash
 # compose/mysql/init/init.sql
 GRANT ALL PRIVILEGES ON mysite4.* TO dbuser@"%" IDENTIFIED BY "123456";
 FLUSH PRIVILEGES;
```

### 编写redis容器配置文件
```bash
 # compose/redis/redis.conf
 # Redis 5配置文件下载地址
 # https://raw.githubusercontent.com/antirez/redis/5.0/redis.conf
 
 # 请注释掉下面一行，变成#bind 127.0.0.1,这样其它机器或容器也可访问
 bind 127.0.0.1
 
 # 取消下行注释，给redis设置登录密码。这个密码django settings.py会用到。
 requirepass 123456
```
### 修改django项目中的settings.py
```python
 # 生产环境设置 Debug = False
 Debug = False
 
 # 设置ALLOWED HOSTS
 ALLOWED_HOSTS = ['your_server_IP', 'your_domain_name']
 
 # 设置STATIC ROOT 和 STATIC URL
 STATIC_ROOT = os.path.join(BASE_DIR, 'static')
 STATIC_URL = "/static/"
 
 # 设置MEDIA ROOT 和 MEDIA URL
 MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
 MEDIA_URL = "/media/"
 
 # 设置数据库。这里用户名和密码必需和docker-compose.yml里mysql环境变量保持一致
 DATABASES = {
     'default': {
         'ENGINE': 'django.db.backends.mysql',
         'NAME': 'mysite4', # 数据库名
         'USER':'dbuser', # 你设置的用户名 - 非root用户
         'PASSWORD':'123456', # # 换成你自己密码
         'HOST': 'db', # 注意：这里使用的是db别名，docker会自动解析成ip
         'PORT':'3306', # 端口
     }
 }
 
 # 设置redis缓存。这里密码为redis.conf里设置的密码
 CACHES = {
     "default": {
         "BACKEND": "django_redis.cache.RedisCache",
         "LOCATION": "redis://redis:6379/1", #这里直接使用redis别名作为host ip地址
         "OPTIONS": {
             "CLIENT_CLASS": "django_redis.client.DefaultClient",
             "PASSWORD": "123456", # 换成你自己密码
         },
     }
 }
```

### 使用Docker-compose构建镜像并启动
```bash
docker-compose build
## 检查images是否成功构建
docker images
## 启动
docker-compose up
## 查看启动的容器
docker ps 
```
### 进入web容器执行start.sh脚本运行uwsgi服务   
如果上一步启动容器没有任何问题的话，就可以执行以下操作了
```bash
docker exec -it mysite4 bash start.sh
```
接下来访问`x.x.x.x:8000`就可以看到上线的项目了。