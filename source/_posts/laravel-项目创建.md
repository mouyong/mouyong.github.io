---
title: laravel 项目创建
date: 2021-01-11 15:59:07
tags:
- Laravel
categories: Laravel
english_title: laravel-project-creation
---

# laravel 项目创建

## 项目立项

- 项目立项并命名: 代运营系统 agent-operation
- 云效建立在线 git 仓库

## Laravel 项目创建

`COMPOSER_MEMORY_LIMIT=-1 composer create-project --prefer-dist laravel/laravel agent-operation`

## 生成 .env 环境配置文件

注：若需要与他人协助共同使用的变量，请在 .env.example 中添加与修改，密码、密钥等信息禁止明文写死在项目中，也禁止写在 .env.example 等会被 git 版本控制的相关环境文件中

1. 修改基础配置

```
APP_NAME=operation-agent
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://operation-agent.test
```

2. 修改数据库配置

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=
```

3. 修改项目运行配置

```
BROADCAST_DRIVER=log
CACHE_DRIVER=file
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120
```

4. 修改缓存配置

```
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

5. 其他配置可根据需要再进行修改


6. 修改项目 .gitignore，添加 .idea/ 目录忽略


## 关联远程仓库

```
cd /path/to/agent-operation

git init .

git add .

git commit -m "feat: 初始化项目"

git remote add origin git@codeup.aliyun.com:xxx/agent-operation/agent-operation.git
```
