---
title: element-ui 表格无限抖动问题及解决
date: 2019-07-03 13:14:14
categories: 开发
tags: 前端
english_title: Infinite-jitter-of-element-ui-table-and-its-solution
---

## 问题

element-ui 是一个不错的前端框架，可以帮助开发者在开发中快速的完成需求。
在开发中，我们的前端 UI 框架也是选择了它。

最近不知道什么时候起，在项目中遇到了一个问题，UI 框架的表格组件开始出现不抖动问题，见下图。

![表格抖动.gif][]

经历了几天的摸索，官方的 github 中也未查到相关信息。后来在官方的网站中看到了 Gitter 聊天室。
在其中找到了答案以及解决方案。

![gitter-ui 位置][]

## 解决过程

摸索排查，怀疑是表格与滚动条一起触发了临界值问题导致表格宽度在无限计算，通过手动调试进行验证，然后确认是这个原因导致的。

![手动调试修复][]

决定直接修改表格宽，但随后否决了这一方案。如果每个用到表格的地方都要加。且都定宽，不太显示。不同的地方展示的数据是不一样的，所以宽度自然也不可能固定。

后继续排查，发现是自动计算是因为表格中每一列使用 `flex: 1` 的方式，自动计算过程中，会导致这个问题的出现。当在 class `.el-main` 中 添加样式 `overflow-x: hidden;` 也能让它不再抖动。

## 问题后续

感觉上面的处理方式不优雅、不完美，便继续查找，后来在官方 gitter 聊天室中，找到了问题的根本原因以及他人的处理方法。

> el-table里有js自动计算整体宽度，75版本的bug会导致flex布局内有滚动条容器内的table在切换显隐时不断计算宽度，差距时17像素左右

这是 chrome 浏览器的 bug，在浏览器 75 版本时会出现。强行解决也行，不过有比较大的副作用。原因是flex导致的，一般是el-main。el-main是flex布局，有 flex:1 属性。而 flex:1 属性在75上就会 bug，需要加 overflow-x：hiddren 进行处理。

但是会带来问题：
加了这个主内容区域就不会有水平滚动条了
对于左侧菜单不横向滚动，主内容区域横向的布局来说是灾难性的

@wxhccc 也提到不建议为了chrome 的 bug 毁了其他浏览器的体验。另外也试过在 el-main 里放 flex:1 的 div，用这个 div 加overflow，但这样会导致el-table宽度变小。用99%，缩小浏览器窗口宽度，也可以解决问题。

@beibeijerry 提到的解决方案是，.el-table 全局添加样式 `width:99.9% !important;` 进行处理。

```
.el-table{
  width:99.9% !important;
}
```

## 解决

最后我选择了 @beibeijerry 提到的方式进行处理。

项目全局为 .el-table 添加样式

```
.el-table{
  width:99.9% !important;
}
```

### 相关讨论

![gitter-image1][]
![gitter-image2][]
![gitter-image3][]
![gitter-image4][]
![gitter-image5][]
![gitter-image6][]
![gitter-image7][]

## 结语

最新的东西不一定是最稳定的。
遇到问题多考虑一下，也许会有更好的处理方式。
Gitter 聊天室可以查看过往讨论信息。
十分感谢 [@zarkin404][] 的协助排查，[@wxhccc][] 与 [@beibeijerry][] 的讨论以及解答。让我能相对较快，较为满意的处理好这个问题。

[@wxhccc]: https://github.com/wxhccc
[@beibeijerry]: https://github.com/beibeijerry
[@zarkin404]: https://github.com/zarkin404

[表格抖动.gif]: 表格抖动.gif
[gitter-ui 位置]: element-ui_gitter.png
[手动调试修复]: manual-fix.png
[gitter-image1]: gitter-resolve-1.png
[gitter-image2]: gitter-resolve-2.png
[gitter-image3]: gitter-resolve-3.png
[gitter-image4]: gitter-resolve-4.png
[gitter-image5]: gitter-resolve-5.png
[gitter-image6]: gitter-resolve-6.png
[gitter-image7]: gitter-resolve-7.png

