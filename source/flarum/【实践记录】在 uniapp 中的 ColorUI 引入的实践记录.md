# 【实践记录】在 uniapp 中的 ColorUI 引入的实践记录

文章编写于 2022年05月19日

## 操作步骤

1. 下载 [colorui uniapp 适配版](https://github.com/Color-UI/coloruiBeta) 到本地。
2. 复制 `coloruiBeta` 目录下的 `/ui`，`/app` 到 `uniapp` 项目中。
3. `/app` 可根据项目进行修改。`/ui` 是 `colorui` 的核心目录，升级时覆盖。
4. 在 `/app/store/index.js` 增加 `setTabbar` 与 `setHomePath` 函数。
5. 在 `/app/store/index.js` 修改 `domain` 与 `apiPath，不同项目的此配置将会不一样。`
6. 在 `/main.js` 中添加 `import store from '@/ui/store'`，完成对 `store` 的引入，并调用 `setTabbar`、`setHomePath`。

```js
import ui from './ui'
import store from '@/ui/store'

const tabbar = [{
    title: '首页',
    icon: '/static/tab_icon/home.png',
    curIcon: '/static/tab_icon/home_cur.png',
    url: '/pages/index/index',
    type: 'tab'
  },
  {
    title: '我的',
    icon: '/static/tab_icon/my.png',
    curIcon: '/static/tab_icon/my_cur.png',
    url: '/pages/mine/mine',
    type: 'tab'
  },
]

store.commit('setTabBar', tabbar)
store.commit('setHomePath', '/pages/index/index')
```

7. 在 `/App.vue` 中引入 `import store from '@/ui/store'`，增加对主题的监听设置，并在 `style` 中引入 公共 `scss`

```js
<script>
  import store from '@/ui/store'
  export default {
    onLaunch: function() {
      uni.hideTabBar();
      // console.log('App Launch')
    },
    onShow: function() {
      // console.log('App Show')
    },
    onHide: function() {
      // console.log('App Hide')
    },
    onThemeChange: function (res){	
      console.log('onThemeChange',res);
      if(store.state.sys_theme == 'auto'){
        store.commit('setStatusStyle', res.theme == 'light' ? 'dark' : 'light');
      }
    }
  }
</script>

<style lang="scss">
  /*每个页面公共css */
  @import '@/ui/scss/ui';
</style>
```