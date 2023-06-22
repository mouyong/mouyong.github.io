# 【实践记录】如何在 laravel 项目中引入 laravel-octane-workerman

文章编写于 2022年04月04日


## 操作步骤

1. 初始化 `laravel` 项目:
`compsoer create-project --prefer-dist laravel/laravel example`
2. 配置 vcs 引入地址:
`composer config repositories.0 vcs https://github.com/mouyong/laravel-octane-workerman`
3. 引入项目
`composer require jie-anthony/laravel-octane-workerman:dev-gatewayworker -vvv`
4. 发布配置文件
`php artisan vendor:publish --provider="JieAnthony\LaravelOctaneWorkerman\WorkermanGatewayWorkerServiceProvider"`
5. 修改 `http` 端口
在配置文件 `config/workerman.php` 中，修改 `key` 为 `gatewayworker.http` 的值为你期望的端口。默认是 `7050`
6. 启动项目
`php artisan workerman:gateway`

## 注意事项

- 默认情况下未启用网关相关功能，网关配置在 `config/workerman.php` 中，键值分别为 `register` `gateway` `business`。
需要修改 `enable` 为启用状态。
- 自定义进程需要自行增加 `enable` 启用状态。
- workerman 文档可前往 [官网](https://www.workerman.net/doc/gateway-worker/) 查看
