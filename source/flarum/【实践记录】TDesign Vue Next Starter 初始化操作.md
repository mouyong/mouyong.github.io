# 【实践记录】TDesign Vue Next Starter 初始化操作

文章编写于 2022年05月01日

## 背景

因计划项目采用 [TDesign](https://tdesign.tencent.com/) 作为前端框架，统一各端项目风格。在此对管理后台的 [Vue3 Starter(Vue Next)](https://tdesign.tencent.com/starter/docs/vue-next/get-started) 项目做一个初始化记录。

## 基础配置

1. 【可选，服务器使用反向代理 ws 时配置】修改 `vite.config.js` 的 `server.hmr.clientPort` 为 `80`
2. 修改 `src/router/models/*.ts` 文件，根目录级别的菜单，隐藏默认的菜单。`meta: { hidden: true, title: '仪表盘', icon: DashboardIcon },`
隐藏入口的原因：新加菜单时，避免受到已有菜单的视觉干扰。
3. 修改 `src/config/global.ts`。`TOKEN_NAME` 为 `localStorage` 中 `默认 token` 的名字，数据为空，见 `src/store/modules/user.ts:11`: `token: localStorage.getItem(TOKEN_NAME) || 'main_token', // 默认token不走权限`。
4. 保留 `src/config/global.ts`。的 `prefix` 配置。`prefix` 为 `class`、`tdesign logo`、`组件名` 的前缀。
5. 修改 `src/config/style.ts` 配置，`showHeader: false`、`showBreadcrumb: true`、`isUseTabsRouter: true`。
6. 修改 `src/layouts/components/Footer.vue` 文件的所有权信息。
7. 修改 `src/layouts/components/SideNav.vue` 文件的项目版本信息，移除项目版本号。内容在 `class="version-container"`。并增加 `hidden` 属性到 `span` 标签。
8. 修改 `index.html` 的 `title` 信息。
9. 替换 Logo 与 icon。


## 项目配置

1. 关闭 `mock`
2. 配置 `src/config/proxy.ts` 中的接口请求地址。当存在 `moke` 地址时，会导致代理地址不生效。
3. 对接接口请求响应信息
4. 对接登录

## 效果

![移除菜单的仪表盘][移除菜单的仪表盘]

: https://laravel-workerman.iwnweb.com/assets/files/2022-05-01/1651391964-675779-6c0c604554380b1f89442fb254702d4.png
