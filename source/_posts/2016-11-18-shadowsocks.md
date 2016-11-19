---
title: shadowsocks
categories: 工具
tags: 工具
date: 2016-11-18 14:59:21
english_title: shadowsocks
---

Shadowsocks
===========

简介
----

Shadowsocks，一个可以快速建立的隧道。在次不做过多的阐述。

安装
----

Debian / Ubuntu:

    sudo apt install python-pip
    pip install shadowsocks

CentOS:

    yum install python-setuptools && easy_install pip
    pip install shadowsocks

安装服务
-------

1. 命令行
    `ssserver -p 443 -k password -m aes-256-cfb`

    要在后台运行：
    `sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody -d start`

    要检查日志：
    `sudo less /var/log/shadowsocks.log`

    通过 `-h` 参数查看所有选项。

2. 配置文件
    ```
    {
        "server": "服务器 ip/域名 ",
        "port_password": {
            "端口": "密码"
        },
        "local_address": "127.0.0.1",
        "localhost_port": 1080,
        "method": "aes-256-cfb"
    }
    ```

* 启动
`/usr/local/bin/ssserver -c /etc/ss.json --pid-file /var/run/shadowsocks.pid --log-file /var/log/shadowsocks.log -d start`

* 将启动命令追加只 `/etc/rc.local`结尾，设为开机自启
`echo "/usr/local/bin/ssserver -c /etc/ss.json --pid-file /var/run/shadowsocks.pid --log-file /var/log/shadowsocks.log -d start" >> /etc/rc.local`


客户端连接
--------

shadowsock 有众多的客户端。

在此只提供下载链接。百度有着许多的配置的相关说明。

[Windows](https://github.com/shadowsocks/shadowsocks-windows/releases)
[下载](https://github.com/shadowsocks/shadowsocks-windows/releases/download/3.3.4/Shadowsocks-3.3.4.zip)

[Linux](https://github.com/shadowsocks/shadowsocks-qt5/releases)
[下载源码](https://github.com/shadowsocks/shadowsocks-qt5/archive/v2.7.0.tar.gz)
`Apt 下载`：
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo update
sudo apt install shadowsocks-qt5
```

[Mac OS](https://github.com/shadowsocks/shadowsocks-iOS/releases)
[下载](https://github.com/shadowsocks/shadowsocks-iOS/releases/download/2.6.3/ShadowsocksX-2.6.3.dmg)

