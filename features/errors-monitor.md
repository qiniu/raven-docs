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

### 报错信息

在报错监控窗页面中，除了可以看到近六小时的报错数量统计直方图以外，还可以分别从报错地址、报错信息、报错类型（WIP）和报错用户三个方面分类地查看报错信息。

**报错地址**

报错地址可以帮助运营人员和开发者快速定位该错误出现的页面和程序所在，也可以快速确定责任人。

**报错消息**

JavaScript 程序报错通常会有两种情况：来自 JavaScript 程序语法和运行时的报错，以及来自业务逻辑的固定报错。为了能让开发者和运营人员能够定位问题的类型，Raven 也提供根据报错信息进行分类查看的报错信息的功能。

点击任一报错地址和报错消息，会弹出该分类条目的相关报错列表，您可以从这里看到更详细的信息。

![](/images/feature-error-monitor1.png)

此处每一条都代表一次独立的报错，点击右侧的“详细”按钮可以查看该条报错消息的详细内容。

![](/images/feature-error-monitor2.png)

点击“查看操作上下文”按钮可以跳到[用户详细操作行为](/features/session)页面查看发生报错的上下文。

**报错用户**

除了“报错地址”和“报错消息”以外，开发者和运营人员还可以通过点击“报错用户”一栏中的用户标识符以查看发生报错的用户近期的会话记录。
