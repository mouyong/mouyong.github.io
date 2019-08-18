---
title: Protocol Buffers 应用之 Laravel 项目创建
date: 2019-08-18 02:03:18
categories: 开发
tags: Protocol-Buffers
english_title: Laravel-Project-Creation-for-Protocol-Buffers-Applications
---

# Protocol Buffers 应用之 Laravel 项目创建

## 前言

经过前面 2 篇文章的铺垫，我们对 Protocol Buffers 已经有了一个初步的了解。接下来写点东西来巩固和加强我们的理解。

Protocol Buffers 只需要定义一次消息协议，便可在不同的语言之间以更少的数据量进行数据的传递。本文我们以一个实际的例子来进行展现说明。

## 正文

### 1. 安装 laravel 框架

接下来我们要创建 Laravel 项目，请打开浏览器访问：https://laravel.com/docs/5.8/installation ，滑动页面来到 Via Composer Create-Project 区块，然后按照下图复制我们的项目创建命令：`composer create-project --prefer-dist laravel/laravel blog` ，并修改项目名为 `learn-protocol-buffers` 。

![复制 laravel 安装命令](复制-laravel-安装命令.png)

打开终端，选择一个目录，粘贴项目创建命令：`composer create-project --prefer-dist laravel/laravel learn-protocol-buffers`，并按下回车执行安装，经过短暂的等待，我们便安装好了项目 `learn-protocol-buffers` 。

![安装项目](安装项目.png)

![项目安装完成](项目安装完成.png)

### 2. 配置项目基本信息

1. 用 PHPStorm 打开项目。


![打开项目](打开项目.png)

2. 编辑 .env 文件中的数据库连接配置部分信息为项目指定的数据库 learn-protobuf

![修改数据库连接配置](修改数据库连接配置.png)

3. 创建数据库 `learn-protobuf`

![新建数据库](新建数据库.png)

![新建数据库-配置数据库信息](新建数据库-配置数据库信息.png)

### 3. 配置服务器信息

1. 新建服务器配置

![新建服务器配置](新建服务器配置.png)

2. 编辑服务器配置（编辑完成后记得重启 web 服务）

![编辑服务器配置](编辑服务器配置.png)

3. 修改 hosts 文件，在文件中添加我们刚刚配置的域名 `learn-protobuf.test`

![添加 hosts 域名记录](添加-hosts-域名记录.png)

### 4. 验证安装结果

请打开浏览器访问 http://learn-protobuf.test 查看安装结果。

![访问项目](访问项目.png)
