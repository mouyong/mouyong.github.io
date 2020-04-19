---
title: 这也许是你期待已久的 laravel 快速上手教程
date: 2020-04-19 07:43:26
tags:
english_title: This-may-be-your-long-awaited-laravel-quick-start-tutorial
---

## 一、Laravel 框架安装

文档：https://learnku.com/docs/laravel/7.x/installation/7447

### 1. 创建项目

`composer create-project --prefer-dist laravel/laravel blog`

### 2. 安装依赖

```
cd blog
composer install
```

### 3. 生成环境配置

#### 1. 修改 .env 信息
```
// 1. 创建环境配置信息
cp .env.example .env

// 2. 生成 APP_KEY
php artisan key:generate

// 3. 修改 .env 信息与敏感信息
# 项目
APP_NAME=Blog
APP_ENV=local
APP_DEBUG=true
APP_URL=http://blog.test

# 数据库
DB_CONNECTION=mysql
DB_HOST=127.0.0.1 # 这里改成数据库主机对应的地址
DB_PORT=3306 # 这里改成数据库对应的端口
DB_DATABASE=blog # 数据库名
DB_USERNAME=username # 用户
DB_PASSWORD=password # 密码

# 全局配置
BROADCAST_DRIVER=log
CACHE_DRIVER=redis
QUEUE_CONNECTION=sync
SESSION_DRIVER=redis
SESSION_LIFETIME=120

# redis 缓存
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
# 默认 db、缓存 db（见 config/database.php redis 部分）
REDIS_DB=0
REDIS_CACHE_DB=1
```

#### 2，创建数据库

1. 创建名为 `blog` 的数据库，指定字符集为 `utf8mb4 -- UTF-8 Unicode` 排序规则为 `utf8mb4_bin`
2. 修改 app/AppServiceProvider.php，处理数据库 utf8mb4 string 默认长度超过索引长度的问题

```
<?php
namespace App\Providers;

use Illuminate\Support\Facades\Schema;
use Illuminate\Support\ServiceProvider;
.
.
.
class AppServiceProvider extends ServiceProvider
{
    .
    .
    .
    public function boot()
    {
        Schema::defaultStringLength(191);
        .
        .
        .
    }
    .
    .
    .
}
```

#### 3. 配置 web 环境

1. 站点配置

```
// 1. 安装 nginx
apt install nginx -y

// 2. 在 nginx 的站点目录中新增 `blog.test.conf`
cp /etc/nginx/site-available/default /etc/nginx/site-available/blog.test.conf

// 3. 修改 blog.test 站点配置
vim /etc/nginx/site-available/blog.test.conf

...
    # 配置访问域名
    server_name blog.test;

    # 配置站点目录
    root "/path/to/blog/public";

    # 美化 url
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # 启用 php-fpm 配置
    ...
...

// 4. 启用站点配置
ln -s /etc/nginx/site-available/blog.test.conf /etc/nginx/site-enable/blog.test.conf

// 5. 检测 nginx 配置是否正确
nginx -t

// 6. 启用站点
nginx -s reload

// 7. 更新 host 信息
echo "blog.test" >> /etc/hosts
```

2. 浏览器访问站点，确认站点已正确启用
http://blog.test

## 二、创建路由

文档：https://learnku.com/docs/laravel/7.x/routing/7458

1. web 路由 routes/web.php

```
.
.
.
Route::get('test', function () {
    return 'this is a test route';
});
.
.
.
```

浏览器访问 http://blog.test/test

2. api 路由 routes/api.php

```
.
.
.
Route::get('test', function () {
    return 'this is an api test route';
});
.
.
.
```

浏览器访问 http://blog.test/api/test

## 三、创建控制器

文档：https://learnku.com/docs/laravel/7.x/controllers/7461

1. 创建 TestController

`php artisan make:controller TestController`。控制器默认目录为 `app/Http/Controllers/`


2. 关联 web 路由

修改 routes/web.php，新增路由

```
.
.
.
Route::get('test/controller', 'TestController@test');
.
.
.
```

修改 routes/api.php，新增路由
```
.
.
.
Route::get('test/controller', 'TestController@testApi');
.
.
.
```

3. 修改控制器 app/Http/Controllers/TestController

```
.
.
.
class TestController extends Controller
{
    public function test()
    {
        return 'this is an web test from test controller';
    }

    public function testApi()
    {
        return response()->json([
            'message' => 'this is an api test from test controller',
        ]);
    }
}
```

3. 浏览器访问

web: http://blog.test/test/controller
api: http://blog.test/api/test/controller

## 模型

### 1. 数据库迁移

文档：https://learnku.com/docs/laravel/7.x/migrations/7496

迁移默认目录为 `database/migrations`

1. 新建迁移
`php artisan make:migration create_tests_table --create tests`

2. 修改迁移（修改已有数据库的表结构）
`php artisan make:migration add_column_test_info_to_tests_table --create tests`

3. 执行迁移

### 2. 模型定义

文档：https://learnku.com/docs/laravel/7.x/eloquent/7499（模型约定，请注意）

模型默认目录为 `app`

创建模型，并指定目录为 `app/Models/`

`php artisan make:model Models/Test`

此时模型 Test 默认关联到数据的 tests 表，可以修改模型属性 $table ，关联其它表。
