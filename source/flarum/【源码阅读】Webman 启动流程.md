# 【源码阅读】Webman 启动流程

文章编写于 2022年04月04日

因为入口命令是 `php start.php start` 所以从 `start.php` 入手查阅源码

- [x] webman bootstrap 加载 @see https://github.com/walkor/webman-framework/blob/master/src/support/bootstrap.php#L75-L87
- [x] start.php => bootstrap.php => 插件bootstrap::start()
- [x] bootstrap.files、plugin 引入
- [x] bootstrap.autoload、plugin 引入
- [x] bootstrap.middleware、plugin 引入
- [x] bootstrap.bootstrap、plugin 引入
- [x] webman plugin 安装机制引入

1. https://github.com/walkor/webman-framework/blob/master/src/start.php php start.php start
2. https://github.com/walkor/webman-framework/blob/master/src/start.php#L87 require_once bootstrap.php
3. https://github.com/walkor/webman-framework/blob/master/src/support/bootstrap.php#L53 autoload.php
4. https://github.com/walkor/webman-framework/blob/master/src/support/bootstrap.php#L60 autoload.files
5. https://github.com/walkor/webman-framework/blob/master/src/support/bootstrap.php#L70 middleware
6. https://github.com/walkor/webman-framework/blob/master/src/support/bootstrap.php#L77 bootstrap::start
7. https://github.com/walkor/webman-framework/blob/master/src/support/bootstrap.php#L84 plugin.bootstrap::start
8. https://github.com/walkor/webman-framework/blob/master/src/start.php#L97 worker_start
9. https://github.com/walkor/webman-framework/blob/master/src/start.php#L102 plugin.worker_start