---
title: ubuntu web 环境的安装
date: 2016-12-01 01:50:35
categories: 环境搭建
tags:
- 工具
- 环境搭建
english_title: ubuntu-web
---
## 安装 web 服务器

### *二者选一即可*

#### 1. apache2

    sudo apt install apache2

#### 2. nginx

    sudo apt install nginx

## 安装数据库 mariadb

    sudo apt install mariadb-server

## 安装 php 以及相关扩展

### 这是 php7.1 的 ppa 源，如有需要，请自行添加

#### `sudo add-apt-repository ppa:ondrej/php`

    sudo apt install php php-zip  php-xml php-mbstring php-redis php-mysql php-gd
