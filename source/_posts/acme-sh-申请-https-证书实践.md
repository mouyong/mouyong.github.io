---
title: acme.sh 申请 https 证书实践
date: 2018-12-02 22:46:37
categories: 环境
tags:
  - nginx
  - 环境
  - 服务器
  - https
  - acme.sh

english_title: Acme.sh-apply-for-https-certificate-practice
---

## 1. 安装 acme.sh

`curl  https://get.acme.sh | sh`

安装完成后会生成 `~/.acme.sh/` 目录
并配置读取 acme.sh.env 中的命令 `. "~/.acme.sh/acme.sh.env"`
我们可以使用 `. ~/.bashrc` 或 `source ~/.bashrc` 让配置立即生效，也可以重新打开一个终端登录服务器。
此时 acme.sh 命令就可以使用了

## 2. 生成证书

`acme.sh --issue -d yourdomain.com --nginx`

更多生成方式参考 [生成证书](https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E#2-%E7%94%9F%E6%88%90%E8%AF%81%E4%B9%A6)

## 3. copy/安装 证书

```
acme.sh  --installcert  -d  <domain>.com   \
        --key-file   /etc/nginx/ssl/<domain>.key \
        --fullchain-file /etc/nginx/ssl/<domain>.cer \
        --reloadcmd  "service nginx force-reload"
```

## 4. 更新 acme.sh

`acme.sh  --upgrade  --auto-upgrade`

## 5. http 跳转 https

![http redirect to https](/images/http2https.png)

```
    if ($scheme = http) {
        return 301 https://$host$request_uri;
    }
```

## 6. nginx https 配置

```
    listen 443 ssl;

    ssl_certificate /etc/nginx/ssl/fullchain.cer;
    ssl_certificate_key /etc/nginx/ssl/www.realomics.cn.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
    ssl_prefer_server_ciphers on;

    if ($scheme = http) {
        return 301 https://$host$request_uri;
    }
```

## 7. 验证 ssl 配置安全性

`https://www.seoptimer.com/your-domain.com`

如果看到 Serurity 是 A+ 就足够了，其他选项可以根据个人需要按照提示进行调整。
