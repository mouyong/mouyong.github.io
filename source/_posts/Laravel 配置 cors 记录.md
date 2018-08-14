---
title: Laravel 配置 cors 记录
date: 2018-08-13 12:57:20
categories: 环境
tags:
  - 前端
  - Laravel
  - CORS

english_title: Laravel-configuring-CORS-record
---

## Laravel 配置 cors 记录

## 配置过程

Package 地址：<https://github.com/barryvdh/laravel-cors>

- 安装

`composer require barryvdh/laravel-cors`

- 配置中间件

```
// \App\Http\Kernel $routeMiddleware 中添加
protected $routeMiddleware = [
    // ...
    'cors' => \Barryvdh\Cors\HandleCors::class,
];

// $middlewareGroups => api => 中添加
'api' => [
    // ...
    'cors',
],
```

- 发布配置文件至 config 目录

`php artisan vendor:publish --provider="Barryvdh\Cors\ServiceProvider"`

- 禁用 API 的 CSRF 保护（可选）

```
// App\Http\Middleware\VerifyCsrfToken
protected $except = [
    'api/*'
];
```