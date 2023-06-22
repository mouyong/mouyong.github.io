# 【实践记录】uniapp request 请求拦截

文章编写于 2022年05月14日

文件保存位置 `js/request.js`
```js
uni.addInterceptor('request', {
  invoke(args) {
    args.dataType = 'json'

    args.header = {
      'Accept': 'application/json',
    }
    if (uni.getStorageSync('token')) {
      args.header['Authorization'] = `Bearer ${uni.getStorageSync('token')}`
    }

    // request 触发前拼接 url 
    const channel = uni.getStorageSync('channel')
    if (!channel) {
      uni.reLaunch({
        url: '/pages/channel/channel',
      })
      return
    }

    const url = args.url.startsWith('/') && args.url.slice(1) || args.url

    let apiUrl = process.env.VUE_APP_API_URL || ''
    if (apiUrl && apiUrl.endsWith('/')) {
      apiUrl = apiUrl.slice(0, -1)
    }

    // 避免重复拼接 url
    if (!url.includes(`://`)) {
      args.url = `${apiUrl}/${channel}/${url}`
    }

    console.info('invoke request', args)
  },
  success(args) {
    console.info('api response', args)

    args.data.code = args.data.err_code || args.statusCode
    args.data.msg = args.data.err_msg || 'unknown error'
    // todo: 文件响应

    // 响应成功
    if (typeof args.data === 'object' && args.data.code === 200) {
      return
    }

    if (typeof args.data === 'object' && args.data.code !== 200) {
      uni.showModal({
        showCancel: false,
        title: '出错了',
        content: args.data.msg,
      })
      // throw new Error(args.data.msg)
      return
    }

    if (args.statusCode >= 400) {
      uni.showModal({
        showCancel: false,
        title: '服务器错误',
      })
      throw new Error('服务器错误')
      return
    }
  },
  fail(err) {
    console.error('interceptor-fail', err)

    if (err.errno !== 0) {
      uni.showModal({
        showCancel: false,
        title: '出错了',
        content: '服务器错误 ' + err.errMsg,
      })
      throw new Error('服务器错误 ' + err.errMsg)
      return
    }
  },
  complete(res) {
    // console.log('interceptor-complete', res)
  }
})
```