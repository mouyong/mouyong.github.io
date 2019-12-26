---
title: 安装 docker 环境
date: 2018-08-13 12:57:20
categories: 环境
tags:
  - docker
  - 服务器

english_title: Installing-docker-environment
---

# 安装 docker 环境

## 安装

- [官方文档](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)

- [阿里云文档](https://help.aliyun.com/document_detail/60742.html?spm=5176.11065259.1996646101.searchclickresult.728f232cP3DvSO)

安装包以允许仓库使用 https

```
sudo apt-get install \
  apt-transport-https \
  ca-certificates \
  curl \
  software-properties-common
```

添加官方 GPG key

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

设置稳定版的仓库源

```
sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"
```

安装 docker-ce

1. 更新仓库源

```
sudo apt-get update
```

2. 安装最新版的 docker-ce

```
sudo apt-get install docker-ce
```

3. 安装指定版本的 docker-ce

  - 查看仓库中可用的版本

```
apt-cache madison docker-ce

docker-ce | 18.03.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```

 - 通过指定版本字符串安装指定版本

```
sudo apt-get install docker-ce=<VERSION>
```

4. 通过运行 hello-world 镜像验证 docker-ce 已被正确安装

```
# sudo docker run hello-world
docker version
```

5. 安装 docker-compose

[参考](https://doc.yonyoucloud.com/doc/docker_practice/compose/install.html)

二进制包

发布的二进制包可以在 https://github.com/docker/compose/releases 找到。

下载后直接放到执行路径即可。

例如，在常见的 Linux 平台上。

```
curl -L https://github.com/docker/compose/releases/download/1.22.0-rc2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

## 换源

- 阿里云

> [容器进行服务控制台](https://cr.console.aliyun.com/?spm=a2c4g.11186623.2.3.s2em6W&accounttraceid=34974d1e-62c7-4738-946c-2ca5345fa292#/imageList)
>
> [镜像加速器](https://cr.console.aliyun.com/?spm=a2c4g.11186623.2.3.s2em6W&accounttraceid=34974d1e-62c7-4738-946c-2ca5345fa292#/accelerator)

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["你的镜像加速地址"],
  "exec-opts": [ "native.cgroupdriver=systemd" ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

- daocloud

> [加速器](https://www.daocloud.io/mirror#accelerator-doc)

## 清理日志

```clean_docker_log.sh
#!/bin/sh

echo "======== start clean docker containers logs ========"

logs=$(find /var/lib/docker/containers/ -name *-json.log)

for log in $logs
        do
                echo "clean logs : $log"
                cat /dev/null > $log
        done

echo "======== end clean docker containers logs ========"
```
