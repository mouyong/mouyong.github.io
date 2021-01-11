---
title: nps server 内网穿透应用搭建与使用
date: 2021-01-09 06:41:13
tags:
- 内网穿透
categories: 内网穿透
english_title: The-construction-and-use-of-NPs-server-intranet-penetration-application
---

# nps server 内网穿透应用搭建与使用

## 下载镜像
```
docker pull ffdfgdfg/nps
```

## 下载配置文件
```
git clone https://github.com/ehang-io/nps /data/nps
```

## 阅读文档修改配置文件

https://ehang-io.github.io/nps/#/example

## 启动
```
docker run -d --name nps --net=host -v /data/nps:/conf ffdfgdfg/nps
```

## 查看日志
```
docker logs nps
```

## 使用

- 服务端
1. 服务端ip:web服务端口（默认为8080，见 conf/nps.conf）
2. 使用用户名和密码登陆（默认admin/123，正式使用一定要更改）
3. 创建客户端

- 客户端
1. 下载客户端安装包并解压，进入到解压目录
2. 点击web管理中客户端前的+号，复制启动命令
3. 修改 conf/npc.conf 相关配置内容
4. 执行启动命令，linux直接执行即可，windows将./npc换成npc.exe用cmd执行
5. 如果需要注册到系统服务(开机启动、守护进程)可查看 [注册到系统服务][] `./npc install`

文档: 域名代理 https://ehang-io.github.io/nps/#/use
```
[common]
server_addr=server_ip:8024
conn_type=tcp
vkey=client_vkey
[agent-operation-api]
host=a.proxy.com
target_addr=127.0.0.1:80
host_change=example.test
header_set_proxy=nps
```


## 参考

[官网]: https://ehang-io.github.io/nps/#/install?id=%e5%ae%89%e8%a3%85%e5%8c%85%e5%ae%89%e8%a3%85
[docker hub]: https://hub.docker.com/r/ffdfgdfg/nps
[github]: https://github.com/ehang-io/nps
[注册到系统服务]: https://ehang-io.github.io/nps/#/use?id=注册到系统服务
