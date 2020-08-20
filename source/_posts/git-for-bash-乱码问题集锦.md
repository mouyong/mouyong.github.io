---
title: git-for-bash 乱码问题集锦
date: 2020-08-20 10:32:12
tags:
- 环境
- windows
english_title: Git-for-bash-garbled-problem
---

# git for bash 乱码问题集锦

- 查看终端编码设置

`LANG c、Character Set utf-8 (unicode)`

- 输入中文乱码

```~/.bashrc
chcp.com 6500

set meta-flag on
set input-meta on
set output-meta on
set convert-meta off

export OUTPUT_CHARSET="utf-8"
export LESSCHARSET="utf-8"
```

- git commit 乱码

```
git config --global i18n.commitencoding utf-8
git config --global i18n.logoutputencoding utf-8
git config --global gui.encoding utf-8
```

## 参考
1. 各种Git Bash乱码解决：https://www.cnblogs.com/steinven/p/10491262.html
2. git乱码解决方案汇总.txt：https://gist.github.com/xkyii/1079783