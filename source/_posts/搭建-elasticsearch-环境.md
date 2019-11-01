---
title: 搭建 elasticsearch 环境
date: 2019-11-02 06:42:54
categories: 搜索引擎
tags: 环境、搜索引擎
english_title: build-elastic-search-environment
---

## 搭建步骤

1. 上传编写好的 docker 版 elasticsearch.zip 至服务器

下载地址：[elasticsearch.zip](/%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E/build-elastic-search-environment/elasticsearch.zip)

`scp elasticsearch.zip root@servername:/usr/local/src`

2. 登录服务器，进入目录 `/usr/local/src`

`cd /usr/local/src`

3. 安装解压命令

`apt install unzip`

4. 解压压缩包

`unzip elasticsearch.zip`

5. 使用 docker 构建 elasticsearch 镜像

`docker build -t elasticsearch /usr/local/src/elasticsearch`

6. 启动 elasticsearch 容器（-Xms1g -Xmx1g：启动时分配内存 1G，运行时最大允许内存分配 1G）

`docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e "ES_JAVA_OPTS=-Xms1g -Xmx1g" elasticsearch`

7. 检查启动结果

`curl 127.0.0.1:9200`

```
{
  "name" : "040d9e6a549a",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "yZ9Vi2_PSpClC5c_j7AVhg",
  "version" : {
    "number" : "7.4.0",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "22e1767283e61a198cb4db791ea66e3f11ab9910",
    "build_date" : "2019-09-27T08:36:48.569419Z",
    "build_snapshot" : false,
    "lucene_version" : "8.2.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

注：**若检查安装结果未出现 elasticsearch 正常输出，检查 elasticsearch 容器日志，并根据日志解决后重新创建容器（若提示内存不足，可尝试降低 -Xms -Xmx 数值后再试，若依然不行，考虑升级机器内存）**
```
docker logs elasticsearch

docker rm elasticsearch

docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e "ES_JAVA_OPTS=-Xms1g -Xmx1g" elasticsearch
```

## 参考
JVM 调优总结 -Xms -Xmx -Xmn -Xss
https://www.cnblogs.com/ceshi2016/p/8447989.html
