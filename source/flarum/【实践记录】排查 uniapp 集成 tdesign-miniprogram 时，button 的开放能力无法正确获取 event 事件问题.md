# 【实践记录】排查 uniapp 集成 tdesign-miniprogram 时，button 的开放能力无法正确获取 event 事件问题

文章编写于 2022年05月08日

## 问题说明：
在 `uniapp` 项目中，使用 `t-button` 的 `open-type` 相关属性 `getPhoneNumber` 时，定义了处理函数 `@getPhoneNumber`，可以正常调用授权，处理函数却无法正确接收到 `event` 对象。导致微信传递的数据无法正常获取

[uniapp 【报Bug】无法获取到手机号](https://ask.dcloud.net.cn/question/144637?item_id=197731&rf=false)

## 排查：
在开发者工具查看 `common/vendor.js` 时，注意到报错，根据报错指引来到下方函数代码。 

当组件是自定义组件，且不是内置组件时，uniapp 对参数的传递做了一点处理，导致无法正确传递 event 对象。
```js
function processEventArgs(vm, event) {var args = arguments.length > 2 && arguments[2] !== undefined ? arguments[2] : [];var extra = arguments.length > 3 && arguments[3] !== undefined ? arguments[3] : [];var isCustom = arguments.length > 4 ? arguments[4] : undefined;var methodName = arguments.length > 5 ? arguments[5] : undefined;
  var isCustomMPEvent = false; // wxcomponent 组件，传递原始 event 对象
  if (isCustom) {// 自定义事件
    isCustomMPEvent = event.currentTarget &&
    event.currentTarget.dataset &&
    event.currentTarget.dataset.comType === 'wx';
    if (!args.length) {// 无参数，直接传入 event 或 detail 数组
      if (isCustomMPEvent) {
        return [event];
      }
      return event.detail.__args__ || event.detail;
    }
  }

  var extraObj = processEventExtra(vm, extra, event);

  var ret = [];
  args.forEach(function (arg) {
    if (arg === '$event') {
      if (methodName === '__set_model' && !isCustom) {// input v-model value
        ret.push(event.target.value);
      } else {
        if (isCustom && !isCustomMPEvent) {

          ret.push(event.detail.__args__[0]);
        } else {// wxcomponent 组件或内置组件
          ret.push(event);
        }
      }
    } else {
      if (Array.isArray(arg) && arg[0] === 'o') {
        ret.push(getObjByArray(arg));
      } else if (typeof arg === 'string' && hasOwn(extraObj, arg)) {
        ret.push(extraObj[arg]);
      } else {
        ret.push(arg);
      }
    }
  });

  return ret;
}
```


## 解决：

通过传递属性 `data-com-type="wx"`，将我们引入的自定义组件声明为内置组件，uniapp 编译后即可正常传递 `event` 事件了。

示例代码：
```js
<template>
  <view class="login-form">
    <t-button data-com-type="wx" open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" block theme="primary" variant="plain">t-button</t-button>  
  </view>
</template>

<script>
  export default {
    data() {
      return {}
    },
    methods: {
      getPhoneNumber(e, eve) {
        console.log('getPhoneNumber', e, eve)
      },
    },
  }
</script>

<style lang="less">
</style>

```
