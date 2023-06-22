# 【实践记录】快速为项目集成 api 接口响应处理

文章编写于 2022年05月05日

## 背景
在框架盛行的时代, 开发接口需求时, 仍需要我们重复的为 `api` 进行响应处理的封装. 在此对 `Laravel` 与 `webman` 框架 `api` 开发进行了简化.

## 实践

### 1. 安装 jwt 包, 并根据文档开发相关 token 的接口处理
- [Larave tymon/jwt-auth 文档](https://jwt-auth.readthedocs.io/en/develop/quick-start/)
`composer require tymon/jwt-auth`

- [webman JWT 认证插件 文档](https://www.workerman.net/plugin/10)
`composer require tinywan/jwt`

### 2. 处理接口响应数据

1. 安装 php-support
`composer require zhenmu/support`

2. 根据文档集成 api 处理到项目中
[文档](https://github.com/mouyong/php-support/): https://github.com/mouyong/php-support/

## 笔者注

笔者认为数据验证 `\validate()->validate()` 没必要从控制器拆分出去。因为大多数项目没有大到为了优雅而优雅的地步。拆分出去既增加时间, 同伴理解难度，还会增加拆分后的开发工作量。若没有必要原因, 不必拆分。