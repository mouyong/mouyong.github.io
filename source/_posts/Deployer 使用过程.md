---
title: Deployer 使用过程
date: 2018-08-13 12:57:20
categories: 环境
tags:
  - 环境

english_title: Deployer-usage-process
---

# Deployer 使用过程

步骤详细解释可见参考 [又一篇 Deployer 的使用攻略](https://laravel-china.org/articles/13242/another-introduction-to-the-use-of-deployer#reply62370)

## 全局安装 deployer

**此部分在本地操作**

`composer global require deployer/deployer -vvv`

查看是否安装成功

`dep --version`

如果提示 `dep` 命令不存在的话，可能需要将 composer 的 bin 目录添加到 PATH 环境变量里面

将 bin 路径添加到 PATH 中

```
# 查看 bin 目录位置
composer global config bin-dir --absolute
# 修改用户配置
vim ~/.bash_profile
# 将 composer bin 目录加到其中即可
# export PATH=/usr/local/bin:/Users/overtrue/.composer/vendor/bin:$PATH
# 然后保存退出
source ~/.basr_profile
```

## 服务端配置

**此部分在目标服务器上操作**

出于安全考虑，添加部署操作的用户

通过 `id` 查看用户 `deployer` 是否已存在

`id deployer`

如果 提示 `id: ‘deployer’: no such user`, 执行以下命令添加用户

`sudo adduser deployer`

设置 `deployer` 用户的登录密码

`sudo passwd deployer`

将用户 deployer 添加至 nginx 默认的用户组。见 nginx.conf 中的 user

```
sudo cat /etc/nginx/nginx.conf | grep user
# user www-data;
# 添加用户到组 sudo usermod -aG 用户组 用户
sudo usermod -aG www-data deployer
```

将 `deployer` 用户权限分别设置为创建文件 644 与目录 755，这样一来，deployer 用户可以读写，但是组与其它用户只能读：

```
su deployer # 切换到 deployer 用户
echo "umask 022" >> ~/.bashrc
exit # 退出
```

将 `depoloyer` 用户加到 sudoers 中：

```
vim /etc/sudoers
# 在最后加入
deployer ALL=(ALL) NOPASSWD: ALL
# 保存并退出
```

对 web 根目录授权。假设我们的 web 服务 项目名叫 `html`，项目的根目录在 `/var/www/` 下，那么需要将这个目录的用户设置为 `deployer` ，组设置为 nginx 用户 `www-data`(之前通过 cat 命令查看到的结果):

`sudo chown deployer:www-data /var/www/html # 最后这里不要加斜线哦(暂时不知道不要加的原因)`

为了让 `deployer` 用户在 `/var/www/html` 下创建的文件与目录集成根目录的权限设定（用户：deployer, 组：www-data），我们还需要一步操作：

`sudo chmod g+s /var/www/html`

## 项目 git 仓库允许服务器访问

**此部分在目标服务器上操作**

我们 deployer 的运行机制是从 git 或者其它你指定的代码库 clone 代码到目标服务器，所以如果你的代码不是公开的仓库，我们通常需要添加 SSH 公钥才可以从代码库 clone 代码，所以接着来创建公钥：

先切换当前登录用户到 deployer：

`su - deployer`

创建 SSH 密钥：

```
ssh-keygen -t rsa -b 4096 -C "deployer"
# 这里的 -C 是指定备注
# 一路回车下去即可
```

然后我们将生成的公钥拷贝出来：

```
cat ~/.ssh/id_rsa.pub # 显示公钥
```

请完整的复制 cat 出来的结果，然后去你的代码库添加 SSH 公钥。

OK, 现在你的服务器就可以从代码库 clone 代码了，你可以在服务器上 git clone 一下你的代码库测试，如果不成功，请检查你的公钥是否正确完全的复制与粘贴正确，不正确的话再次重复复制粘贴即可。

## 服务器免密码登录 deployer

**此部分在本地（或者开发机）操作**

在本地（或者开发机）执行部署任务时我们不想每次输入密码，所以我们需要将 deployer 用户设置 SSH 免密码登录：

在本机生成 deployer 专用密钥，然后拷贝公钥：

`ssh-keygen -t rsa -b 4096 -f  ~/.ssh/deployerkey`

然后将公钥保存到目标服务器（注意，这一步还是在本机操作）：

```
ssh-copy-id -i  ~/.ssh/deployerkey.pub deployer@123.45.67.89 # 请填写服务器 IP
# 应该会让你输入 deployer 在服务器上的登录密码，输入后回车即可
```

然后你应该就可以直接以 deployer 用户免密码登录到服务器了，测试方式：

```
ssh deployer@123.45.67.89 -i ~/.ssh/deployerkey
# 应该就能直接进到服务器上了，然后 exit 退出
```

## Deployer 的使用

**这些都在本地操作哦**

假设我们的项目在本地 /www/demo-project 下，那么我们切换到该目录：

`cd /www/demo-project`

然后执行 Deployer 的初始化命令：

`dep init`

它会让你选择项目类型，比如 Laravel，symfony 等，如果你都不是，选择 common 类型即可。

这一步操作将会在当前目录生成一个 deploy.php 文件，这个文件就是部署清单，也就是告诉 Deployer 怎样去部署你的项目，关于这部分我们不需要过多的介绍，大家去参考 Deployer 官网的详细说明操作即可。

需要关心的几个配置是：

```
// 指定你的代码所在的服务器 SSH 地址，请不要使用 https 方式哦。
set('repository', 'git@mygitserver.com:overtrue/demo-project.git');

// 这里填写目标服务器的 IP 或者域名
host('your_server_ip')
    ->user('deployer') // 这里填写 deployer
      // 并指定公钥的位置
    ->identityFile('~/.ssh/deployerkey')
    // 指定项目部署到服务器上的哪个目录
    ->set('deploy_path', '/var/www/demo-app');
```

执行部署
`dep deploy -vvv`

## 添加任务

```deployer.php
set('bin/npm', function () {
    return run('which npm');
});

desc('Install npm packages');
task('npm:install', function () {
    if (has('previous_release')) {
        if (test('[ -d {{previous_release}}/node_modules ]')) {
            run('cp -R {{previous_release}}/node_modules {{release_path}}');
        }
    }
    run("cd {{release_path}} && {{bin/npm}} install");
});
after('deploy:symlink', 'npm:install');

desc('npm build assets');
task('npm:run:prod', function () {
    run('cd {{release_path}} && sudo npm run prod');
});
after('npm:install', 'npm:run:prod');

desc('Restart PHP-FPM service');
task('php-fpm:restart', function () {
    run('sudo systemctl restart php-fpm.service');
});
after('npm:run:prod', 'php-fpm:restart');
```

## 注意

```
  [Symfony\Component\Process\Exception\RuntimeException]
  TTY mode is not supported on Windows platform.
```

TTY 模式 不支持在 windows 使用，在 deployer.php 中修改配置

```
set('git_tty', true);
to
set('git_tty', false);
```

```
In Client.php line 99:

  [Deployer\Exception\RuntimeException (-1)]
  The command "cd /var/www/omics/releases/1 && (getfacl -p storage/framework/views | grep "^user:nginx:.*w" | wc -l)" failed.

  Exit Code: -1 (Unknown error)

  Host Name: omics.cblink.net

  ================
  mm_send_fd: sendmsg(2): Connection reset by peer
  mux_client_request_session: send fds failed
```

```
# 参考 issue https://github.com/deployphp/deployer/issues/1270#issuecomment-340270151 评论，windows 需要禁止 ssh 多路复用
set('ssh_multiplexing', true);
to
set('ssh_multiplexing', false);
```

```
In Client.php line 99:

  [Deployer\Exception\RuntimeException (1)]
  The command "/usr/bin/php /var/www/demo-app/releases/1/artisan migrate --force" failed.

  Exit Code: 1 (General error)

  Host Name: omics.cblink.net

  ================

  In Connection.php line 664:

    SQLSTATE[HY000] [1044] Access denied for user ''@'localhost' to database 'f
    orge' (SQL: select * from information_schema.tables where table_schema = fo
    rge and table_name = migrations)


  In Connector.php line 68:

    SQLSTATE[HY000] [1044] Access denied for user ''@'localhost' to database 'f
    orge'
```

部署报错。因为没有 .env 配置文件，无法执行迁移。

```
以 deployer 用户登录服务器
ssh deployer@123.45.67.89
# 到服务器的项目部署目录中删除默认生成的 .env
rm /var/www/demo-app/shared/.env
# copy 一份可用的 .env 配置至 shared/ 中
cp /var/www/demo-app/{release/.env.example,shared/.env}
# 如果不是以 deployer 用户执行的，请注意修改文件权限
sudo chown -R deployer:www-data /var/www/demp-app
```