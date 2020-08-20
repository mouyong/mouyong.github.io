---
title: laravel 集成 mongodb 记录
date: 2020-08-20 10:37:47
tags:
english_title: Laravel-integration-mongodb-record
---

# laravel 集成 mongodb 记录

## 1. 安装

`composer require jenssegers/mongodb`

## 2. 在 config/databases.php 添加 mongodb 配置

```
'mongodb' => [
    'driver' => 'mongodb',
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', 27017),
    'database' => env('DB_DATABASE', 'homestead'),
    'username' => env('DB_USERNAME', 'homestead'),
    'password' => env('DB_PASSWORD', 'secret'),
    'options' => [
        // here you can pass more settings to the Mongo Driver Manager
        // see https://www.php.net/manual/en/mongodb-driver-manager.construct.php under "Uri Options" for a list of complete parameters that you can use

        'database' => env('DB_AUTHENTICATION_DATABASE', 'admin'), // required with Mongo 3+
    ],
],
```

## 3. mongodb 添加用户信息

```
use admin
db.auth("username", "password")
db.createUser({user: "your-username", pwd: "password", roles: [{role: "dbOwner", db: "database"}]})

示例:
use admin
db.auth("root", "example")
db.createUser({user: "test", pwd: "123456", roles: [{role: "dbOwner", db: "test"}]})
```

## 4. 配置数据库环境

```
# mongodb production
DB_CONNECTION=mongodb
DB_HOST=ecs.iwnweb.com
DB_PORT=27017
DB_DATABASE=question-dev
DB_USERNAME=question-dev
DB_PASSWORD=123456
```

## 5. 修改 User 模型的继承类

```
use Jenssegers\Mongodb\Auth\User as Authenticatable;

class User extends Authenticatable
{

}
```

## 6. 修改 model.stub 的继承类

1). 发布 stubs 文件
参考： https://learnku.com/docs/laravel/7.x/artisan/7480#stub-customization

`php artisan stub:publish`

2). 修改 model.stub

```
namespace {{ namespace }};

use Jenssegers\Mongodb\Eloquent\Model;

class {{ class }} extends Model
{
    //
}
```

## 7. 文档

Laravel MongoDB: https://github.com/jenssegers/laravel-mongodb
