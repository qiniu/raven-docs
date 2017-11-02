### 下载 Raven 仓库对应的 JavaScript 文件

为了保证 Raven 服务使用的直观和简单，开发者可以直接在 Raven Portal 的[控制台](https://raven.qiniu.com/#/dashboard)中查看 Raven 仓库并下载对应的 Raven JavaScript SDK 文件。

![](images/install1.png)

这个 Raven JavaScript SDK 文件包含对应 Raven 仓库的相关配置，只能用于将数据写入到该 Raven 仓库中。开发者可以选择将其上传到七牛云对象存储并通过七牛云融合 CDN 来将其以最快的速度提供给各地的用户。

### 引入 Raven JavaScript SDK 到网站页面

确保了下载好的 Raven JavaScript SDK 文件已经可以在公网访问后，便可以将其引入到网站的页面上去。默认配置下 Raven 的引入非常简单，只需要将其使用 `script` 标签引入到页面上即可。

```html
<script type="application/javascript" src="/path/to/raven.js"></script>
```

需要特别注意的是请勿在这个 `script` 标签上加入 `async` 属性，否则不能保证 Raven 服务的正常运行。
