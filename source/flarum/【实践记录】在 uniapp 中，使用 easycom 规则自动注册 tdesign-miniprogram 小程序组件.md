# 【实践记录】在 uniapp 中，使用 easycom 规则自动注册 tdesign-miniprogram 小程序组件

文章编写于 2022年05月08日

## 背景
自从决定全端引入 tdesign 体系后，小程序组件自然也不落下的采用。

在前端领域中，组件分为全局注册和按需引入注册。

全局注册所有组件，对项目来说有点多余，会有很多组件暂时用不着。较为推荐的是在需要的页面按需引入组件。

原生小程序按需引入组件，是在页面的 xxx_page.json 中定义 "{components: true, usingComponents: {组件: "组件代码源文件"}}"，示例如下：
```json
{
  "navigationBarTitleText": "登录",
  "usingComponents": {
    "t-button": "/wxcomponents/tdesign-miniprogram/button/button"
  }
}
```

## 问题

[easycom 是否会考虑支持自动扫描 js 后缀的组件？tdesign 小程序组件是 js 后缀的。
](https://ask.dcloud.net.cn/question/144635)

如果所有的组件我们都要自己引入，是十分繁琐的一件事。

uniapp 有 [easycom](https://uniapp.dcloud.io/collocation/pages.html#easycom) 的组件引入方式，故采用其为我们自动配置按需组件。

下方是 `tdesign-miniprogram` 的 `easycom` 配置。

## 解决

注意：
- step-item 无法通过t-(.*) 规则进行注册。所以需要单独引入规则。
- 如果有 `tdesign-miniprogram` 其他组件需要自行引入规则，请写在通用规则前面。
- `tdesign-miniprogram` 组件资源需要保存在 `/wxcomponents/` 目录下。见[小程序自定义组件支持](https://uniapp.dcloud.io/tutorial/miniprogram-subject.html)
```js

  "easycom": {
    "autoscan": true,
    "custom": {
      "^m-(.*)": "@/components/$1/$1",
      "^mt-(.*)": "@/wxcomponents/tdesign-miniprogram/$1s/$1-item", // step-item
      "^t-(.*)": "@/wxcomponents/tdesign-miniprogram/$1/$1"
    }
  }
```

npm 包
```js
···
"easycom": {
    "autoscan": true,
    "custom": {
      "^m-(.*)": "@/components/$1/$1",
      "^mt-(.*)": "tdesign-miniprogram/$1s/$1-item", // step-item
      "^t-(.*)": "tdesign-miniprogram/$1/$1"
    }
  }
···
```