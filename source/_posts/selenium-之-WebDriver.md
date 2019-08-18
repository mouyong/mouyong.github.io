---
title: Selenium 之 WebDriver
date: 2019-08-18 09:27:41
categories: 测试
tags: Selenium
english_title: WebDriver-of-Selenium
---

## 前言

你是否遇到过如下场景呢？

- 打开特定的网站进行一些特定的操作。
- 开发时，使用浏览器对页面进行访问，查看效果。

本文我们简单记录下关于以上问题的一种处理方案（Selenium）相关的驱动 WebDriver 的安装。

## 简介

Selenium 是一个用于 Web 应用程序测试的工具，Selenium 测试直接运行在浏览器中，就像真正的用户在操作一样（P.S. 模拟用户做事的「人」）。

## 正文

> 工欲善其事，必先利其器。-- 孔子（春秋）《论语・卫灵公》

### 1. 下载

打开浏览器，访问 [selenium 下载页:https://docs.seleniumhq.org/download/](https://docs.seleniumhq.org/download/) ，滑动鼠标来到 Third Party Drivers, Bindings, and Plugins 区块，下载自己浏览器对应的 Selenium webdriver 驱动。

![Google Chrome webdriver 下载](webdriver-download.gif)

### 2. 解压并配置环境变量

将 webdriver 解压至 c:\webdrivers 目录中，然后在环境变量 PATH 中添加 C:\webdrivers 。

![Google Chrome webdriver 解压并配置环境变量 PATH](webdriver-path.gif)

### 3. 验证安装结果

打开命令行 cmd，输入 `chromedriver.exe --version`，查看是否有版本输出。

![Google Chrome webdriver 验证安装结果](webdriver-check.gif)
