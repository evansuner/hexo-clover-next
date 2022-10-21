---
title: Docker2-常见软件安装
date: 2022-01-04 05:39:28.674
updated: 2022-01-04 05:41:31.136
url: /?p=102
categories: 
- 服务器
tags: 
- docker
---

**安装nginx**
```bash
# 拉取镜像并启动
~ docker pull nginx
~ docker run -itd --name nginx-test -p 80:80 nginx

# 在宿主机创建以下目录
~ mkdir -p /usr/local/nginx
~ cd /usr/local/nginx
~ mkdir html conf logs

# 将容器内部的配置文件、日志以及静态文件映射到宿主机
docker cp nginx-test:/usr/etc/nginx/conf.d /usr/local/nginx/conf
docker cp nginx-test:/usr/etc/nginx/nginx.conf /usr/local/nginx/conf

# 接下来关闭并删除nginx-test
docker stop nginx-test
docker rm nginx-test

# 使用数据卷的形式重新创建nginx容器
docker run -itd --name nginx-80 -p 80:80 \
-v /usr/local/nginx/html:/usr/share/nginx/html \
-v /usr/local/nginx/conf/conf.d/default.conf:/etc/nginx/conf.d/default.conf \
-v /usr/local/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /usr/local/nginx/logs:/var/log/nginx nginx

# 使用nginx对网站进行负载均衡
# 编辑conf/cond.d/default.conf文件
vim default.conf

# 在server前面添加
upstream nginxCluster{
    server ip:8080;
    server ip:8081;
}
# 在server中添加
location /xx{
    proxy_pass http://nginxCluster;
}
# 重启nginx
docker restart nginx-80
```

**安装MySQL5.7**
```bash
# 拉取镜像
~ docker pull mysql:5.7
# 启动容器并配置密码
~ docker run -itd --name mysql5.7-3306 -p 3306:3306 \
-e MYSQL_ROOT_PASSWORD='xxxxxx' mysql:5.7
```
[mysql5.7配置](https://www.reidosann.top/?p=24)，这里不再赘述。

**安装Redis**
```bash
# 拉取redis镜像
~ docker pull redis
# 启动容器并配置requirepass
~ docker run -itd --name redis4.x-6379 -p 6379:6379 redis:4.0.1 --requirepass "xxxxxx"
```