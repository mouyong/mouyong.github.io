---
title: Laravel 集成 GraphQL
date: 2020-08-31 16:48:25
category: Laravel
tags:
- Laravel
english_title: Laravel-integrated-graphql
---

# Laravel 集成 GraphQL

## 集成指引

### 1. 安装 `nuwave/lighthouse`
`composer require nuwave/lighthouse`

### 2. 安装在线调试工具 `mll-lab/laravel-graphql-playground`
`composer require mll-lab/laravel-graphql-playground`

### 3. 发布默认模式与相关配置
`php artisan vendor:publish --provider="Nuwave\Lighthouse\LighthouseServiceProvider"`

会在相应目录中生成相关文件：`config/lighthouse.php`、`graphql/schema.graphql`

### 4. 引入自定义 `php` 标量 `mll-lab/graphql-php-scalars`
`composer require mll-lab/graphql-php-scalars`

### 5. 更新 `graphql/schema.graphql` 文件

1). 创建 `graphql/traits` 目录

2). 更新 `graphql/schema.graphql` 文件

```
"A datetime string with format `Y-m-d H:i:s`, e.g. `2018-01-01 13:00:00`."
scalar DateTime @scalar(class: "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\DateTime")

"A date string with format `Y-m-d`, e.g. `2011-05-23`."
scalar Date @scalar(class: "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\Date")

"A datetime string with format `Y-m-d H:i:s`, e.g. `2018-05-23 13:43:32`."
scalar DateTime @scalar(class: "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\DateTime")

scalar Email @scalar(class: "MLL\\GraphQLScalars\\Email")

scalar JSON @scalar(class: "MLL\\GraphQLScalars\\JSON")

scalar Mixed @scalar(class: "MLL\\GraphQLScalars\\Mixed")

type Query
type Mutation

#import traits/*.graphql
```

3). 创建 `traits/users.graphql` 文件

```
extend type Query {
    users: [User!]! @paginate(defaultCount: 10)
    user(id: ID @eq): User @find
}

type User {
    id: ID!
    name: String!
    email: String!
    created_at: DateTime!
    updated_at: DateTime!
}
```

## 文档
文档：http://lighthouse-php.cn/master/security/validation.html#validate-fields