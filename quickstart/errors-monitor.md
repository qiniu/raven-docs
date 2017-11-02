### 提交错误

Raven JavaScript SDK 默认会拦截 JavaScript 程序中未被拦截的报错（通过 `window.onerror`），若开发者需要将一些已被拦截的错误上报到 Raven 服务，则需要在 JavaScript 程序中集中处理错误的代码中加入 Raven 的相关代码。

`raven.captureExpection(error: Error)` 方法可以对错误对象进行收集处理。该方法只能传入一个 `Error` 对象（或其他 `Error` 类型），若错误信息只有一个字符串，请使用 `new Error(errorMessage)` 创建一个错误对象。

```javascript
handleErrorOccur((err) => {
  // ...

  if (raven) {
    raven.captureExpection(err)
  }
})

// String Error Message
const errorMessage = 'Error message from server'

if (raven) {
  raven.captureExpection(new Error(errorMessage))
}
```

### 报错监控

登陆 Raven Portal，在 Raven 仓库页面中，可以看到近六小时内的报错数量统计直方图以及近三小时内的报错位置信息。点击“三小时报错监控窗”按钮可跳转到更为详细的报错监控窗页面。

![](images/quick-error-monitor2.png)

在**报错监控窗**页面中，开发者可以看到近三小时内该 Raven 仓库接收到的报错记录，以及详细的报错 URL、错误信息以及报错用户等。

![](images/quick-error-monitor1.png)
