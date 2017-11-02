### 配置用户信息

Raven 服务支持针对个体用户的行为进行记录和跟踪，但必须要由开发者在程序中为 Raven 服务提供当前操作用户的识别标识（如 UID）才可完成跟踪记录。

在用户完成登陆以后开发者可以使用 `raven.setUserContext(UID: string)` 进行用户信息配置。

> 出于避免 Raven 文件引入状况（如 Raven 文件加载失败）所带来的隐患，在使用 `raven` 对象之前，都请务必先检查该对象是否存在并可用。

```javascript
afterUserLogined((uid) => {
  // ...

  if (raven) {
    raven.setUserContext(uid)
  }
})
```

### 查看指定用户行为记录

登陆 Raven Portal，打开需要查询的 Raven 仓库，在左侧导航栏中选择 “用户行为” - “指定用户”，在查询框中输入需要查询用户的标识并点击“搜索”按钮。

![](images/user-behavior-start1.png)

这个页面中可以浏览到该用户的一些基本信息：如最后操作时间、最后浏览页面、常用浏览器、常用操作系统、常驻地区等。开发者可以通过下方的“最近二十次会话记录”查询该用户最近登录后的行为记录。