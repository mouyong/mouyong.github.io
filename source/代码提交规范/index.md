---
title: 代码提交规范
date: 2020-05-14 10:33:51
categories: 环境
tags: 开发

english_title: Code-submission-specification
---
## 问题

• 每个人风格不同，格式凌乱，查看很不方便
• 部分 commit 没有填写 message，事后难以得知对应修改的作用

## 意义
• 方便快速浏览查找，回溯之前的工作内容
• 可以直接从commit 生成Change log(发布时用于说明版本差异)
规范Commit message不仅能解决上述问题，而且基本没有副作用和学习成本，应该尽早加上。

规范方式
• 使用 commitlint 和 husky 来进行提交检查。当执行 git commit 时会在对应的git钩子上执行相关校验命令，只有符合格式的Commit message才能提交成功。
• 增加了 commitizen 支持，使用 cz-customizable 进行配置。支持使用git cz替代git commit
Commit message 格式

每次提交，Commit message 都包括三个部分：Header，Body 和 Footer。

<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>

其中，Header 是必需的，Body 和 Footer 可以省略。

不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）。这是为了避免自动换行影响美观。
scope 选填表示 commit 的作用范围，如数据层、视图层，也可以是目录名称 subject必填用于对commit进行简短的描述 type

必填表示提交类型，值有以下几种：
• feat - 新功能 feature
• fix - 修复 bug
• docs - 文档注释
• style - 代码格式(不影响代码运行的变动)
• refactor - 重构、优化(既不增加新功能，也不是修复bug)
• perf - 性能优化
• test - 增加测试
• chore - 构建过程或辅助工具的变动
• revert - 回退
• build - 打包

## 使用

### 1. 如何加入项目

#### commitlint（commit 检验工具）

```
// commitlint
// 项目目录下安装
npm i commitlint --save-dev
npm i @commitlint/config-conventional --save-dev
// 在项目目录下，新建配置文件 commitlint.config.js， 内容如下
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    // type 类型定义
    'type-enum': [2, 'always', [
      "feat", // 新功能 feature
      "fix", // 修复 bug
      "docs", // 文档注释
      "style", // 代码格式(不影响代码运行的变动)
      "refactor", // 重构(既不增加新功能，也不是修复bug)
      "perf", // 性能优化
      "test", // 增加测试
      "chore", // 构建过程或辅助工具的变动
      "revert", // 回退
      "build" // 打包
    ]],
    // subject 大小写不做校验
    // 自动部署的BUILD ROBOT的commit信息大写，以作区别
    'subject-case': [0]
  }
};
```

#### husky（npm 的 Git 钩子管理工具）

```
// husky
// 项目目录下安装
npm i husky --save-dev
// 在package.json文件中增加相关配置
"husky": {
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }
}
```

#### commitizen（一个撰写合格 Commit message 的工具）

```
// commitizen
// 项目目录下安装
npm install commitizen --save-dev
npm install cz-customizable --save-dev
// 在package.json文件中增加相关配置
// 注意这里的path可能要根据实际情况进行修改，如nAdmin项目
"config": {
  "commitizen": {
    "path": "./node_modules/cz-customizable"
  }
}
// 在项目目录下，新建配置文件 .cz-config.js
'use strict';
module.exports = {
  types: [
    {value: 'feat',     name: 'feat:     新功能'},
    {value: 'fix',      name: 'fix:      修复'},
    {value: 'docs',     name: 'docs:     文档变更'},
    {value: 'style',    name: 'style:    代码格式(不影响代码运行的变动)'},
    {value: 'refactor', name: 'refactor: 重构(既不是增加feature，也不是修复bug)'},
    {value: 'perf',     name: 'perf:     性能优化'},
    {value: 'test',     name: 'test:     增加测试'},
    {value: 'chore',    name: 'chore:    构建过程或辅助工具的变动'},
    {value: 'revert',   name: 'revert:   回退'},
    {value: 'build',    name: 'build:    打包'}
  ],
  // override the messages, defaults are as follows
  messages: {
    type: '请选择提交类型:',
    // scope: '请输入文件修改范围(可选):',
    // used if allowCustomScopes is true
    customScope: '请输入修改范围(可选):',
    subject: '请简要描述提交(必填):',
    body: '请输入详细描述(可选，待优化去除，跳过即可):',
    // breaking: 'List any BREAKING CHANGES (optional):\n',
    footer: '请输入要关闭的issue(待优化去除，跳过即可):',
    confirmCommit: '确认使用以上信息提交？(y/n/e/h)'
  },
  allowCustomScopes: true,
  // allowBreakingChanges: ['feat', 'fix'],
  skipQuestions: ['body', 'footer'],
  // limit subject length, commitlint默认是72
  subjectLimit: 72
};
```

### 2. 自动生成 Change log

使用 npm run changelog 自动生成CHANGELOG.md文件。
规范了Commit格式之后，发布新版本时，使用 Conventional Changelog 就能够自动生成Change log，生成的文档包括以下3个部分：
• New features
• Bug fixes
• Breaking changes(不向上兼容的部分，我们的规范不要求footer，所以这一项不会出现)

每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。当然，生成的文档允许手动修改，所以发布前，你还可以添加其他内容

```
// 安装
npm i conventional-changelog-cli --save-dev
// 在package.json中加入配置方便使用
"scripts": {
  "changelog": "node_modules/.bin/conventional-changelog -p angular -i CHANGELOG.md -s"
}
// 项目目录下生成 CHANGELOG.md 文件
npm run changelog
```

### 3. 配置完成后其他人如何使用

1. 将配置测试完成的代码合并至主分支。
2. 其他开发者需要重新执行 npm i 安装相关依赖。
3. 如果要使用 commitizen，需要全局安装 npm i commitizen -g

FAQ:
[大家的问题以及处理](https://juejin.im/post/5c828a45f265da2de52dbcfa#heading-7)

## 参考

- Commit message 和 Change log 编写指南: http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html
- 约定式提交: https://www.conventionalcommits.org/zh-hans/v1.0.0-beta.4/
- 超详细的Git提交规范引入指南: https://juejin.im/post/5c828a45f265da2de52dbcfa