---
title: Violentmonkey 暴力猴
date: 2019-07-09 09:20:21
categories: 工具
tags: 脚本
english_title: violentmonkey
---

## 简介
Violentmonkey - 暴力猴，界面简洁，美观，而不失优雅的扩展。
它是一个为浏览器提供用户脚本支持的插件。
只要会一点前段知识，你便可通过轻松掌握它，扩展网页功能，满足自己的需要。

## 支持的浏览器

Chrome|Chromium|Opera 15+|QQBrowser|Firefox 57+|...
---|---|---|---|---|---|---

## 安装

你可以点击[这里](https://violentmonkey.github.io/get-it/)查看你的浏览器对应的扩展下载地址。

## API

你可以点击[这里](https://violentmonkey.github.io/api/)查看支持的 API 如何使用。

## 使用

背景
在使用 GitLab 关联 TAPD 时，我们会对 webhooks 进行设置，选择我们需要的事件进行通知。其中触发 webhook 通知的事件非常多，默认只开启了 Push event。我希望所有的项目关联操作都可以直接与 TAPD 关联上，那么需要对每一个事件都进行勾选。这是一件繁琐的事情，我们可以交给 Violentmonkey 来帮我们完成。

脚本
```
// ==UserScript==
// @name Gitlab 集成 event
// @namespace mouyong
// @description 集成自动勾选所有选择复选 event
// @match https://git.cblink.net/*/hooks*
// @match https://git.cblink.net/*/settings/integrations
// @license LGPL
// @grant none
// @version 0.0.1.20190709034435
// ==/UserScript==
document.querySelectorAll("input[name*='events']").forEach(function (_) {
    _.setAttribute('checked', true);
});
```

所有的元都必须为以下格式
```
// ==UserScript==
// @key value
// ==/UserScript==
```

通过 `@name` 字段，我们设置了脚本名称。然后在 `@namespace` 中声明了脚本的命名空间，这样可以保证脚本名不会与他人的脚本重复。`@description` 字段，简短的介绍了脚本的作用，我们能清晰的知道这个脚本是干嘛用的。`@match` 告诉浏览器，在哪些网页中需要执行脚本，扩展我们的功能；此处我们需要填写的是新增与修改 webhooks 时的地址。不同项目与不同 hook 的地址是不一样的，所以用 `*` 来代替所有。关于 `@license` 是提供授权用规则的。可以查看[如何选择开源许可证？](http://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html)了解你应该选择什么授权。我选择了 LGPL 授权规则，因为我希望有变动时，我能知道，这样可以共同完善脚本功能。`@grant` 字段告诉浏览器我们的脚本需要哪些权限，这个功能不需要权限，所以指定为 none。`@version` 能方便用户升级脚本。所以建议按照[语义化版本 2.0.0](https://semver.org/lang/zh-CN/) 提供一个版本号。

我们的脚本只有一行内容，查找页面中的所有事件多选框，并一个一个的选中它们。

## 发布

点击[这里](https://greasyfork.org/zh-CN)打开用户脚本网站，点击登录。
登录后点击退出旁边的用户名，点击提交脚本，我们将脚本内容粘贴到多行输入框中，点击上方的对编辑器启用语法高亮，可以看到是否存在简单的语法问题。这里发布的脚本要尽可能的用 es5 的语法来写，因为当前还有部分浏览器不支持 es6 语法。脚本类型选择一栏，我们适合自己脚本的类型即可。提交页面中的相关信息填写完毕后，便可以发布脚本了。

点击前往安装[Gitlab 集成 event](https://greasyfork.org/zh-CN/scripts/387315-gitlab-%E9%9B%86%E6%88%90-event/code) 脚本
