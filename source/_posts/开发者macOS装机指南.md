---
title: 开发者macOS装机指南
date: 2022-10-08 17:42:44.324
updated: 2022-10-17 11:24:50.741
url: /?p=369
categories: 
- MacOS
tags: 
---

## ClashX Pro

优先配置虚拟专用网

## Command Line Tools for Xcode

```bash
xcode-select --install
```

## Homebrew

```bash
# 配置环境变量
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles"

# 安装homebrew
## 途径1 使用清华的国内镜像源
git clone --depth=1 https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/install.git brew-install
/bin/bash brew-install/install.sh
rm -rf brew-install
## 途径2 from github, 前提是配置好外网
/bin/bash -c "$(curl -fsSL https://github.com/Homebrew/install/raw/master/install.sh)"

# 针对Apple Silicon 单独配置
test -r ~/.bash_profile && echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.bash_profile
test -r ~/.zprofile && echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile

# 替换上有仓库
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
brew update
## 使用一条命令自动设置
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
for tap in core cask{,-fonts,-drivers,-versions} command-not-found; do
    brew tap --custom-remote --force-auto-update "homebrew/${tap}" "https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-${tap}.git"
done
brew update
```

## Docker

```bash
# 安装 
brew install -appdir=/Applications docker

# 配置
#  打开application -> preference -> Docker Engine -> 添加下列内容
"insecure-registries": [
    "https://mirror.ccs.tencentyun.com",
    "https://docker.mirrors.ustc.edu.cn"
  ]
# 打开application -> preference -> General -> use Docker Compose V2
# 打开application -> preference -> General -> Start Docekr Desktop when you log in
```

## Nodejs

```bash
# 安装
brew install node

# 测试
node -v                              
v16.15.1
npm -v                
8.19.2

# 配置
## 镜像加速
npm config set registry https://registry.npmmirror.com
sudo npm i yarn -g
yarn config set registry https://registry.npmmirror.com
## 版本控制
sudo npm i n -g
n install 16.15.1
```

## Oh-My-ZSH

```bash
# 安装
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 配置
## 关闭自动更新
zstyle ':omz:update' mode disabled
## 插件安装
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

## Python

```bash
# 检查当前版本
python --version

# python版本控制
brew install pyenv

# 虚拟环境安装
## 配置pip3 国内镜像源
pip3 config set global.index-url=global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
## pyenv-virtualenv安装
### 1. This is the recommended method of installation if you installed pyenv with Homebrew.
brew install pyenv-virtualenv
### 2. After installation, you'll still need to do Pyenv shell setup steps then add.
eval "$(pyenv virtualenv-init -)"
#### 使用
pyenv virtualenv 3.6.8 virtual-env-36  # 创建指定python版本虚拟环境
pyenv virtualenv venv38  # 创建当前python版本虚拟环境

pyenv activate <name>  # 激活指定虚拟环境
pyenv deactivate  # 退出当前虚拟环境

pyenv virtualenvs  # 打印所有的虚拟环境

pyenv uninstall virtual-env-36  # 卸载虚拟环境
```

## MongoDB & MySQL & Redis & Nginx

```bash
# 编辑docker-compose.yml
version: '3'

services:
  nginx:
    image: nginx:1
    container_name: nginx
    restart: always
    ports:
      - 80:80
      - 443:443
    volumes:
      - ~/Workspace:/var/www
      - ~/Documents/Logs/nginx:/var/log/nginx
      - ~/Documents/Conf/nginx:/etc/nginx/conf.d

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: always
    ports:
      - 3307:3306
    volumes:
      - ~/Documents/Logs/mysql:/var/log/mysql
      - ~/Documents/Data/mysql:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=123456

  redis:
    image: redis:6
    container_name: redis
    restart: always
    command: redis-server --requirepass 123456
    ports:
      - 6379:6379
    volumes:
      - ~/Documents/Data/redis:/data

  mongo:
    image: mongo:4
    container_name: mongo
    restart: always
    ports:
      - 27017:27017
    volumes:
      - ~/Documents/Data/mongo:/data/db
      - ~/Documents/Logs/mongo:/var/log/mongodb
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: 123456
```
## Picgo 
```bash
sudo npm i -g picgo
vim ~/.picgo/config.json

{
  "picBed": {
    "uploader": "upyun",
    "current": "upyun",
    "upyun": {
      "bucket": "xxx",
      "operator": "xxx",
      "password": "xxx",
      "url": "xxx",
      "options": "",
      "path": "/xxx"
    },
    "transformer": "path"
  },
  "picgoPlugins": {}
}
```
> Typora 配置图片自动上传
> ![](http://cdn-images.reidosann.top/CDN/image-20221017112410779.png)


## 开发工具推荐

+ Navicat
+ Warp
+ Visual Studio Code
+ PyCharm
+ WebStorm
+ RDM
+ Postman
+ ClashX Pro
+ Typora