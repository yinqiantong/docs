# API 文档

**目录**

* [签名](#签名)
* [创建订单接口](#创建订单接口)
* [查询订单接口](#查询订单接口)
* [支付回调通知](#支付回调通知)

## 签名

* [签名说明](https://github.com/yinqiantong/docs/blob/master/doc/sign.md)
* [签名测试](https://yinqiantong.com/html/experience/sign.html)

## 创建订单接口

[POST] `https://yinqiantong.com/order`

```
用于创建支付订单
```

### header 参数

| 参数 | 类型 | 是否必填 | 描述 |
| :---: | :---: | :---: | --- |
| appid | string | 必填 | **银钱通** App ID（可到「**银钱通**管理后台」-「应用管理」-「应用列表」中查看） |
| appkey | string | 必填 | **银钱通** App Key （可到「**银钱通**管理后台」-「应用管理」-「应用列表」中查看） |
| ts | int | 必填 | 当前时间戳，10位，取值范围：`ts > 当前时间戳-86400秒` && `ts < 当前时间戳+86400秒` |
| sign | string | 必填 | 接口签名。[签名方法](https://github.com/yinqiantong/docs/blob/master/doc/sign.md) |

### body 参数

| 参数 | 类型 | 是否必填 | 描述 |
| :---: | :---: | :---: | --- |
| client_out_trade_no | string | 必填 | 商户自定义订单号，要求32个字符内，只能是数字、大小写字母，且在同一app内唯一 |
| channel | string | 必填 | 支付渠道，`wx`：微信；`alipay`：支付宝 |
| platform | string | 必填 | 支付平台，`app`：手机支付；`mp`：公众号支付；`mini_app`：小程序支付；`h5`：H5支付；`pc`：PC网页支付，`scan`：扫码支付 |
| money | int | 必填 | 支付金额，以 `分` 为单位（1元=100分）。取值范围：`money>0` |
| client_ip | string | 必填 | 客户端IP，发客户端IP，发起支付请求客户端的 IPv4 地址，如: `114.114.114.114` |
| notify_url | string | 必填 | 异步接收微信支付结果通知的回调地址，用户支付成功后，我们服务器会主动发送一个post消息到这个网址，请求内容请参照[支付回调通知](#支付回调通知) |
| open_id | string | 某些条件下必填 | 微信 open id，微信公众号/微信小程序必填此参数 |
| return_url | string | | 返回页面地址，支付宝PC支付和微信H5支付支持，当用户支付完成后，返回的页面，返回的时候会自动带上url参数 `out_trade_no` |
| subject | string | | 商品标题 |
| description | string | | 商品描述 |
| extra | string | | 额外参数，支付成功后，原样返回 |

### 返回参数

| 参数 | 类型  | 描述 |
| :--- | :---: | --- |
| code | int | 状态码 |
| msg | string | 提示信息 |
| data | json object |  |
| data.app_id | string | **银钱通** App ID |
| data.channel | string | 支付渠道，`wx`：微信；`alipay`：支付宝 |
| data.platform | string | 支付平台，`app`：手机支付；`mp`：公众号支付；`mini_app`：小程序支付；`h5`：H5支付；`pc`：PC网页支付，`scan`：扫码支付 |
| data.out_trade_no | string | **银钱通** 全局唯一的订单号 |
| data.cient_out_trade_no | string | 商户自定义订单号 |
| data.status | string | 订单状态，`unpaid`：未支付；`paid`：支付成功；`expired`：已失效；`refunding`：正在退款；`refunded`：已退款 |
| data.money | int | 订单金额，以 `分` 为单位（1元=100分） |
| data.client_ip | string | 客户端IP，发起支付请求客户端的 IPv4 地址，如: `114.114.114.114` |
| data.open_id | string | 微信 open id |
| data.subject | string | 商品标题 |
| data.description | string | 商品描述 |
| data.extra | string | 额外参数 |
| data.pay_body | string | 支付相关的请求内容 |
| data.pay_time | long | 支付时间戳 10 位，创建订单的时候，默认 0 |
| data.create_time | long | 订单创建时间戳 10 位 |
| data.expire_time | long | 订单过期时间戳 10 位 |

### 请求例子

> 请求

```
curl -X POST -H 'Content-type: application/json' \
    -H 'ts: 1536200462' \
    -H 'appid: 00000000' \
    -H 'appkey: 1234567890123456' \
    -H 'sign: 74f9cca4418240b2340e36991d44e672' \
    -d '{"client_out_trade_no":"1540449058","channel":"wx", "platform":"h5","money":1,"client_ip":"127.0.0.1","notify_url":"https://yinqiantong.com/test","subject":"商品标题","description":"商品描述","extra":"this is extra param"}' \
    'https://yinqiantong.com/order'
```

> 返回例子

```
{
  "msg": "ok",
  "code": 200,
  "data": {
    "app_id": "00000000",
    "channel": "wx",
    "platform": "h5",
    "out_trade_no": "00000000231540451777acf9947",
    "client_out_trade_no": "1540449058",
    "status": "unpaid",
    "money": 1,
    "client_ip": "127.0.0.1",
    "open_id": "",
    "subject": "商品标题",
    "description": "商品描述",
    "notify_url": "https://yinqiantong.com/test",
    "return_url": "",
    "extra": "this is extra param",
    "pay_body": "https://wx.tenpay.com/cgi-bin/mmpayweb-bin/checkmweb?prepay_id=wx25151618125264917b9fc0de4142376373&package=1479459154&redirect_url=",
    "pay_time": 0,
    "expire_time": 1540458977,
    "create_time": 1540451777
  }
}
```

## 查询订单接口

[GET] `https://yinqiantong.com/order`

```
用于查询订单的详情信息
```

### url参数

| 参数 | 类型 | 是否必填 | 描述 |
| :---: | :---: | :---: | --- |
| appid | string | 必填 | **银钱通** App ID |
| out_trade_no | string | | **银钱通** 全局唯一的订单号 |
| client_out_trade_no | string | | 商户自定义订单号 |

### 返回参数

| 参数 | 类型  | 描述 |
| :--- | :---: | --- |
| code | int | 状态码 |
| msg | string | 提示信息 |
| data | json object |  |
| data.app_id | string | **银钱通** App ID |
| data.channel | string | 支付渠道，`wx`：微信；`alipay`：支付宝 |
| data.platform | string | 支付平台，`app`：手机支付；`mp`：公众号支付；`mini_app`：小程序支付；`h5`：H5支付；`pc`：PC网页支付，`scan`：扫码支付 |
| data.out_trade_no | string | **银钱通** 全局唯一的订单号 |
| data.cient_out_trade_no | string | 商户自定义订单号 |
| data.status | string | 订单状态，`unpaid`：未支付；`paid`：支付成功；`expired`：已失效；`refunding`：正在退款；`refunded`：已退款 |
| data.money | int | 订单金额，以 `分` 为单位（1元=100分） |
| data.client_ip | string | 客户端IP，发起支付请求客户端的 IPv4 地址，如: `114.114.114.114` |
| data.open_id | string | 微信 open id |
| data.subject | string | 商品标题 |
| data.description | string | 商品描述 |
| data.extra | string | 额外参数 |
| data.pay_body | string | 支付相关的请求内容 |
| data.pay_time | long | 支付时间戳 10 位 |
| data.create_time | long | 订单创建时间戳 10 位 |
| data.expire_time | long | 订单过期时间戳 10 位 |

### 请求例子

> 请求

```
curl 'https://yinqiantong.com/order?out_trade_no=00000000231540451777acf9947&appid=00000000' | jq
curl 'https://yinqiantong.com/order?client_out_trade_no=1540449058&appid=00000000' | jq
```

> 返回例子

```
{
  "msg": "ok",
  "code": 200,
  "data": {
    "app_id": "00000000",
    "channel": "wx",
    "platform": "h5",
    "out_trade_no": "00000000231540451777acf9947",
    "client_out_trade_no": "1540449058",
    "status": "unpaid",
    "money": 1,
    "client_ip": "127.0.0.1",
    "open_id": "",
    "subject": "商品标题",
    "description": "商品描述",
    "notify_url": "https://yinqiantong.com/test",
    "return_url": "",
    "extra": "this is extra param",
    "pay_body": "https://wx.tenpay.com/cgi-bin/mmpayweb-bin/checkmweb?prepay_id=wx25151618125264917b9fc0de4142376373&package=1479459154&redirect_url=",
    "pay_time": 0,
    "expire_time": 1540458977,
    "create_time": 1540451777
  }
}
```

## 支付回调通知

```
在用户支付成功后，我们根据您在创建订单时候的 notify_url 参数，会把支付结果回调给商户
```

### 通知Header

| 参数 | 类型 | 描述 |
| :---: | :---: |  --- |
| appid | string | **银钱通** App ID ，请校验本地的appid是否一致 |
| appkey | string | **银钱通** App Key 请校验本地的appkey是否一致 |
| ts | int | 当前时间戳，10位，请校验 `ts > 当前时间戳-86400秒` && `ts < 当前时间戳+86400秒` 条件是否成立 |
| sign | string | 接口签名，请校验签名，[签名方法](https://github.com/yinqiantong/docs/blob/master/doc/sign.md) |

### 通知body

**Content-type: application/json**

| 参数 | 类型 | 描述 |
| :---: | :---: |  --- |
| app_id | string | **银钱通** App ID |
| channel | string | 支付渠道，`wx`：微信；`alipay`：支付宝 |
| platform | string | 支付平台，`app`：手机支付；`mp`：公众号支付；`mini_app`：小程序支付；`h5`：H5支付；`pc`：PC网页支付，`scan`：扫码支付 |
| out_trade_no | string | **银钱通** 全局唯一的订单号 |
| cient_out_trade_no | string | 商户自定义订单号 |
| status | string | 订单状态，`unpaid`：未支付；`paid`：支付成功；`expired`：已失效；`refunding`：正在退款；`refunded`：已退款 |
| money | int | 订单金额，以 `分` 为单位（1元=100分） |
| client_ip | string | 客户端IP，发起支付请求客户端的 IPv4 地址，如: `114.114.114.114` |
| open_id | string | 微信 open id |
| subject | string | 商品标题 |
| description | string | 商品描述 |
| extra | string | 额外参数 |
| pay_time | long | 支付时间戳 10 位 |

### 返回参数

```
收到支付结果通知后，请严格按照示例返回参数给银钱通
```

| 参数 | 类型 | 描述 |
| :---: | :---: |  --- |
| code | int | 200 代表成功，若出现请求异常，或者code不等于 200 的情况，银钱通会不定时地回调 |

返回例子

```
{
    "code": 200
}
```
