---
title: 为 sublime 配置 NodeJs 的编译环境
date: 2016-08-14 10:25:52
categories: 环境
tags:
  - sublime
  - NodeJs
english_title: nodejs-sublime
---
在sublie text下编辑nodeJs的文件后，笔者希望能直接编译并查看效果，为此笔者google后做了如下操作：
==================

打开sublime，找到菜单栏的`tools(工具)`，点开，点击`Build System`，在右边选择`New Build System...`，在新建立的文件中输入如下内容：
{% blockquote %}
{
    "cmd": ["node", "$file"],
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.js",
    "shell_cmd": "clear & taskkill /F /IM node.exe & node \"$file\"",
    "encoding": "utf-8",
}
{% endblockquote %}
接着我将以上代码保存在`D:\Sublime Text\Data\Packages\User`的“NodeJsJavascript.sublime-build”(**我的Sublime Text放在D盘**)，然后回到`tool(工具)->Build system`，选择刚刚新建立的`的“NodeJsJavascript`，接着就可以开心敲node.js的代码了，以后每次要编译查看效果的时候，只需要按快捷键`Ctrl+B`就可以查看了。
**如果你在命令行中遇到如下错误**![错误][]
这是因为8000端口被之前在sublime编译时的程序占用着的。
你需要在命令行中执行`taskkill /f /t /im node.exe`(我为了省事，使用了通配符`*`)就可以终止node.exe程序。如果你的8000不是因为sublime编译时运行的node程序导致的，请执行以下步骤查询是谁占用了端口，并杀死(结束)这个进程。
说明：*findstr查询字符串*
`netstat -ano|findstr 8000`
回车后可以看到占用此端口的进程的PID(*在最后的数字*)我的占用进程的PID是16352。
接着执行
`tasklist|findstr 16352`，回车后你就可以看到是哪个程序占用了这个端口。我的是node.exe。
最后执行
`taskkill /f /t /im node.exe`我就杀死了这个进程了。

然后在命令行重新执行就可以了。
执行过程如下：
![结束进程][]


[错误]: err.png "编译报错"
[结束进程]: kill.png "手动查找进程 PID 并杀死进程"
