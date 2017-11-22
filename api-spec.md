## API 接收地址

`https://raven.qiniu.com`

## API 请求内容

若 Raven API 需要提交相关内容，均需要以 `application/json` 作为 `Content-Type` 请求头内容，并以 JSON（JavaScript Object Notation） 为请求内容格式进行。

## API 返回内容

Raven API 返回内容全部以 JSON 为格式。

* 如果请求成功,返回HTTP状态码200:

    ```
    HTTP/1.1 200 OK
    ```
* 如果请求失败,返回包含如下内容的JSON字符串（已格式化,便于阅读）:

    ```json
    {
        "error": "<errMsg string>"
    }
    ```

* 如果请求包含数据获取,则返回相应数据的JSON字符串；

## 鉴权

Raven API 使用七牛云公开的鉴权方式，需要使用七牛云账号的密钥以生成单次使用的 QiniuToken，详细的签名算法请参考：[https://developer.qiniu.com/dora/kb/3702/QiniuToken](https://developer.qiniu.com/dora/kb/3702/QiniuToken)。

得到 QiniuToken 后，将以 `Qiniu ` 为开头的字符串加入到需要访问 API 的 `Authorization` 请求头中以完成 Raven API 的鉴权。

---

## 仓库 API

Raven 的数据统计分析服务围绕着 Raven 服务仓库进行，用户可以在 [Raven Portal](https://raven.qiniu.com) 上进行仓库的创建，也可以通过 API 进行创建和管理。

### 创建仓库

* 请求方法： `POST`
* 请求地址： `/api/v1/repos/<RepoName>`

**请求语法**

```
POST /api/v1/repos/<RepoName>
Content-Type: application/json
Authorization: Qiniu <auth>
```

**请求内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 创建仓库的名称。可由字母，下划线开头，3 ~ 128 个字符长，可包含字母、数字、下划线。 |

**响应报文**

```json
{
  "code": 200,
  "data": {
    "message": "OK",
    "repoName": "<RepoName string>"
  }
}
```

**示例**

```bash
curl -X POST https://raven.qiniu.com/api/v1/repos/test_repo \
-H 'Content-Type: application/json' \
-H 'Authorization: Qiniu auth_token'
```

### 获取指定仓库

* 请求方法： `GET`
* 请求地址： `/api/v1/repos/<RepoName>`

**请求语法**

```
GET /api/v1/repos/<RepoName>
Authorization: Qiniu <auth>
```

**请求内容**


| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库的名称。可由字母，下划线开头，3 ~ 128 个字符长，可包含字母、数字、下划线。 |

**响应报文**

```json
{
  "code": 200,
  "data": {
    "_id": "<RepoID>",
    "name": "<RepoName>",
    "createdAt": "<CreateTime>",
    "stats": {
      "currentPoints": "<CurrentMessagesCount>",
      "historyPoints": "<HistoryMessagesCount>"
    }
  }
}
```

**返回内容**


| 参数 | 类型 | 说明 |
| --- | --- | --- |
| RepoID | `string` | 仓库的 ID，为一个哈希字符串 |
| RepoName | `string` | 仓库名称 |
| CreateTime | `string` | 仓库创建时间，格式为 ISO 时间字符串 |
| CurrentMessagesCount | `number` | 当前数据量（两天内） |
| HistoryMessagesCount | `number` | 历史数据量 |


**示例**

```bash
curl -G https://raven.qiniu.com/api/v1/repos/test_repo \
-H 'Authorization: Qiniu auth_token'
```

### 删除指定仓库

* 请求方法： `DELETE`
* 请求地址： `/api/v1/repos/<RepoName>`

**请求语法**

```
DELETE /api/v1/repos/<RepoName>
Authorization: Qiniu <auth>
```

**请求内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库的名称。可由字母，下划线开头，3 ~ 128 个字符长，可包含字母、数字、下划线。 |

**示例**

```bash
curl -X DELETE https://raven.qiniu.com/api/v1/repos/test_repo \
-H 'Authorization: Qiniu auth_token'
```

### 获取当前账号中的所有仓库信息

* 请求方法： `GET`
* 请求地址： `/api/v1/repos`

**请求语法**

```
GET /api/v1/repos
Authorization: Qiniu <auth>
```

**响应报文**

```json
{
  "code": 200,
  "data": [
    {
      "_id": "<RepoID>",
      "name": "<RepoName>",
      "createdAt": "<CreateTime>",
      "stats": {
        "currentPoints": "<CurrentMessagesCount>",
        "historyPoints": "<HistoryMessagesCount>"
      }
    },
    ...
  ]
}
```

**返回内容**

请参考[获取指定仓库]()。

**示例**

```bash
curl -G https://raven.qiniu.com/api/v1/repos \
-H 'Authorization: Qiniu auth_token'
```

### 向指定仓库收集数据

* 请求方法： `POST`
* 请求地址： `/api/v1/repos/<RepoName>/collect`

**请求语法**

```
POST /api/v1/repos/<RepoName>/collect
Content-Type: application/json
Authorization: Qiniu <auth>
[
  {
    "type": "<Type>",
    "category": "<Category>",
    "payload": <Payload>,
    "user": "<User>",
    "tags": <Tags>,
    "extra": <Extra>,
    "environment": "<Environment>",
    "release": "<Release>",
    "url": "<Url>",
    "session": "<Session>"
  },
  ...
]
```

**请求内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| Type | `string` | 是 | 消息类型。可选 `message` 和 `error` |
| Category | `string` | 是 | 类型分类 |
| Payload | `object` | 是 | 消息内容 |
| User | `string` | 否 | 用户标识符 |
| Tags | `array`/`object` | 否 | 标签信息 |
| Extra | `object` | 否 | 额外信息 |
| Environment | `string` | 是 | 环境标识 |
| Release | `string` | 否 | 版本信息 |
| Url | `string` | 是 | 当前页面 URL 地址 |
| Session | `string` | 是 | 当前会话标识符 |

**示例**

```bash
curl -X POST https://raven.qiniu.com/api/v1/repos/test_repo/collect \
-H 'Content-Type: application/json' \
-H 'Authorization: Qiniu auth_token' \
-d '[
  {
    "type": "message",
    "category": "navigation",
    "payload": {
      "from": "/foo"
    },
    "user": "<User>",
    "tags": <Tags>,
    "extra": <Extra>,
    "environment": "<Environment>",
    "release": "<Release>",
    "url": "<Url>",
    "session": "<Session>"
  }
]'
```

## 用户行为 API

当 Raven 仓库已经接收到来自 [Raven JavaScript SDK](https://github.com/qiniu/raven) 或者 API 所收集到的数据并完成了必要的数据处理后，便可以通过 Raven 的用户行为 API 进行数据获取。


### 获取用户行为记录

* 请求方法： `GET`
* 请求地址： `/api/v1/repos/<RepoName>/actions`

**请求语法**

```
GET /api/v1/repos/<RepoName>/actions?<Options>
Authorization: Qiniu <auth>
```

**请求内容**


| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库名称 |
| user | `string` | 否 | 指定查询某一用户 |
| startTime | `string` | 是 | 查询记录的开始时间 |
| endTime | `string` | 是 | 查询记录的结束时间 |
| category | `category` | 否 | 指定查询某一类型的记录 |

**响应报文**

```json
{
  "code": 200,
  "data": [
    {
      "type": "<Type>",
      "category": "<Category>",
      "payload": <Payload>,
      "user": "<User>",
      "tags": <Tags>,
      "extra": <Extra>,
      "environment": "<Environment>",
      "release": "<Release>",
      "url": "<Url>",
      "session": "<Session>",
      "timestamp": "<Timestamp>",
      "ip": "<IP>",
      "country": "<Country>",
      "province": "<Province>",
      "city": "<City>",
      "user_group": "<UserGroup>"
    },
    ...
  ]
}
```

**返回内容**

| 参数 | 类型 | 必有 | 说明 |
| --- | --- | --- | --- |
| Type | `string` | 是 | 消息类型 |
| Category | `string` | 是 | 类型分类 |
| Payload | `object` | 是 | 消息内容 |
| User | `string` | 否 | 用户标识符 |
| Tags | `array`/`object` | 否 | 标签信息 |
| Extra | `object` | 否 | 额外信息 |
| Environment | `string` | 是 | 环境标识 |
| Release | `string` | 否 | 版本信息 |
| Url | `string` | 是 | 当前页面 URL 地址 |
| Session | `string` | 是 | 当前会话标识符 |
| Timestamp | `string` | 是 | 消息时间帧。以 ISO 字符串为格式 |
| IP | `string` | 是 | 发送方 IP 地址 |
| Country | `string` | 否 | 发送方地理位置。国家级 |
| Province | `string` | 否 | 发送方地理位置。省/州级 |
| City | `string` | 否 | 发送方地理位置。城市级 |
| UserGroup | `string` | 否 | 用户群检测结果 |

**示例**

```bash
curl -G https://raven.qiniu.com/api/v1/repos/test_repo/actions?startTime=2017-11-22T04%3A29%3A18.679Z&endTime=2017-11-22T10%3A29%3A18.681Z \
-H 'Authorization: Qiniu auth_token'
```

### 获取指定 URL 目录下的用户行为记录

* 请求方法： `GET`
* 请求地址： `/api/v1/repos/<RepoName>/actions/url/<EncodedURL>`

**请求语法**

```
GET /api/v1/repos/<RepoName>/actions/url/<EncodedURL>?<Options>
Authorization: Qiniu <auth>
```

**请求内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库名称 |
| EncodedURL | `string` | 是 | 在 JavaScript 中使用 `encodeURIComponent` 转义后的 URL 目录地址，如 `/test` 则转义为 `%2Ftest` |
| user | `string` | 否 | 指定查询某一用户 |
| startTime | `string` | 是 | 查询记录的开始时间 |
| endTime | `string` | 是 | 查询记录的结束时间 |
| category | `category` | 否 | 指定查询某一类型的记录 |

**示例**

```bash
curl -G https://raven.qiniu.com/api/v1/repos/test_repo/actions/url/%2Ftest?startTime=2017-11-22T04%3A29%3A18.679Z&endTime=2017-11-22T10%3A29%3A18.681Z \
-H 'Authorization: Qiniu auth_token'
```

### 获取页面跳转行为记录

* 请求方法： `GET`
* 请求地址： `/api/v1/repos/<RepoName>/actions/navigations`

**请求语法**

```
GET /api/v1/repos/<RepoName>/actions/navigations?<Options>
Authorization: Qiniu <auth>
```

**请求内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库名称 |
| user | `string` | 否 | 指定查询某一用户 |
| startTime | `string` | 是 | 查询记录的开始时间 |
| endTime | `string` | 是 | 查询记录的结束时间 |

**示例**

```bash
curl -G https://raven.qiniu.com/api/v1/repos/test_repo/actions/navigations?startTime=2017-11-22T04%3A29%3A18.679Z&endTime=2017-11-22T10%3A29%3A18.681Z \
-H 'Authorization: Qiniu auth_token'
```

### 获取仓库内的会话信息

* 请求方法： `GET`
* 请求地址： `/api/v1/repos/<RepoName>/actions/sessions`

**请求语法**

```
GET /api/v1/repos/<RepoName>/actions/sessions?<Options>
Authorization: Qiniu <auth>
```

**请求内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库名称 |
| user | `string` | 否 | 指定查询某一用户 |
| startTime | `string` | 是 | 查询记录的开始时间 |
| endTime | `string` | 是 | 查询记录的结束时间 |
| category | `category` | 否 | 指定查询某一类型的记录 |

**响应报文**

```json
{
  "code": 200,
  "data": [
    {
      "session": "<SessionID>",
      "start": "<StartTime>",
      "end": "<EndTime>",
      "duration": "<Duration>",
      "actions": <Actions>
    },
    ...
  ]
}
```

**返回内容**

| 参数 | 类型 | 必有 | 说明 |
| --- | --- | --- | --- |
| SessionID | `string` | 是 | 该次会话的标识符 |
| StartTime | `string` | 是 | 该次会话的开始时间 |
| EndTime | `string` | 是 | 该次会话的结束时间 |
| Duration | `number` | 是 | 该次会话的持续时间（毫秒） |
| Actions | `array` | 是 | 该次会话中的用户行为记录 |

**示例**

```bash
curl -G https://raven.qiniu.com/api/v1/repos/test_repo/actions/sessions?startTime=2017-11-22T04%3A29%3A18.679Z&endTime=2017-11-22T10%3A29%3A18.681Z \
-H 'Authorization: Qiniu auth_token'
```

## 用户群 API

Raven 服务提供用户群机制，Raven 用户可以通过 Raven API 来进行仓库中用户群设置的创建和管理。

### 创建用户群

* 请求方法： `POST`
* 请求地址： `/api/v1/repos/<RepoName>/user-groups`

**请求语法**

```
POST /api/v1/repos/<RepoName>/user-groups
Content-Type: application/json
Authorization: Qiniu <auth>
{
  "name": "<GroupName>",
  "value": "<GroupValue>",
  "path": "<Path>"
}
```

**请求内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库名称 |
| GroupName | `string` | 是 | 用户群名称 |
| GroupValue | `string` | 是 | 用户群对应检索值 |
| Path | `string` | 是 | 用户群信息在消息数据中检索路径，如 `extra.user_group` |

**注意！**

在同一个仓库中所有的用户群都必须使用相同的消息数据检索路径，否则在创建中会报错。
如将检索路径设置为 `extra.user_group` 时，在 Raven JavaScript SDK 中则需要设置：

```javascript
raven.setExtraContext({
  user_group: 'group_a'
})
```

**示例**

```bash
curl -X POST https://raven.qiniu.com/api/v1/repos/test_group/user-groups \
-H "Content-Type=application/json" \
-H "Authorization=Qiniu auth_token" \
-d '{
  "name": "Group A",
  "value": "group_a",
  "path": "extra.user_group"
}'
```

### 获取指定仓库的用户群

* 请求方法： `GET`
* 请求地址： `/api/v1/repos/<RepoName>/user-groups`

**请求语法**

```
GET /api/v1/repos/<RepoName>/user-groups
Authorization: Qiniu <auth>
```

**请求内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库名称 |

**响应报文**

```json
{
  "code": 200,
  "data": [
    {
      "_id": "<GroupID>",
      "name": "<GroupName>",
      "value": "<GroupValue>",
      "path": "<Path>"
    },
    ...
  ]
}
```

**返回内容**

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| RepoName | `string` | 是 | 仓库名称 |
| GroupID | `string` | 是 | 用户群 ID |
| GroupName | `string` | 是 | 用户群名称 |
| GroupValue | `string` | 是 | 用户群对应检索值 |
| Path | `string` | 是 | 用户群信息在消息数据中检索路径 |

**示例**

```bash
curl -G https://raven.qiniu.com/api/v1/repos/test_repo/user-groups \
-H "Authorization=Qiniu auth_token"
```


