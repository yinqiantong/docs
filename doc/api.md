# API 文档

**目录**

* [签名](#签名)
* [错误码](#错误码)
* [一，创建订单接口](#一创建订单接口)
* [二，查询订单接口](#二查询订单接口)
* [三，支付回调通知](#三支付回调通知)

## 签名

* [签名说明](https://github.com/yinqiantong/docs/blob/master/doc/sign.md)
* [签名测试](https://yinqiantong.com/html/experience/sign.html)

## 一，创建订单接口

[POST] `https://api.yqtapi.com/order`

```
用于创建支付订单
```

### body 参数

| 参数 | 类型 | 是否必填 | 描述 |
| :---: | :---: | :---: | --- |
| appid | string | 必填 | **银钱通** App ID（可到「**银钱通**管理后台」-「应用管理」-「应用列表」中查看） |
| ts | int | 必填 | 当前时间戳，10位，取值范围：`ts > 当前时间戳-86400秒` && `ts < 当前时间戳+86400秒` |
| sign | string | 必填 | 接口签名。[签名方法](https://github.com/yinqiantong/docs/blob/master/doc/sign.md) |
| client_out_trade_no | string | 必填 | 商户自定义订单号，要求32个字符内，只能是数字、大小写字母，且在同一app内唯一 |
| channel | string | 必填 | 支付渠道，`1`：微信；`2`：支付宝；`3`：QQ；`4`：京东 |
| platform | string | 必填 | 支付平台，`1`：PC网页支付，`2`：手机支付；`3`：H5支付；`4`：公众号支付；`5`：小程序支付；`6`：扫码支付 |
| money | int | 必填 | 支付金额，以 `分` 为单位（1元=100分）。取值范围：`money>0` |
| client_ip | string | 必填 | （用户）发起支付请求客户端的 IPv4 地址，如: `114.114.114.114`，注意：这里不是服务器的 IP |
| notify_url | string | 必填 | 异步接收微信支付结果通知的回调地址，要求128个字符内，用户支付成功后，我们服务器会主动发送一个post消息到这个网址，请求内容请参照[支付回调通知](#支付回调通知) |
| open_id | string | 某些条件下必填 | 微信 open id，微信公众号/微信小程序必填此参数 |
| return_url | string | | 返回页面地址，要求128字符内，不能带参数，支付宝PC支付和微信H5支付支持，当用户支付完成后，返回的页面，返回的时候会自动带上url参数 `out_trade_no` |
| subject | string | | 商品标题，要求32个字符内，如：银钱通会员 |
| description | string | | 商品描述，要求128个字符内，如：银钱通充值中心-会员充值 |
| extra | string | | 额外参数，支付成功后，原样返回，要求512个字符内 |

### 返回参数

| 参数 | 类型  | 描述 |
| :--- | :---: | --- |
| code | int | 状态码，请参考状态码说明 |
| msg | string | 提示信息 |
| data | json object |  |
| data.pay_body | string | 支付相关的凭证 |
| data.out_trade_no | string | **银钱通** 唯一订单号 |
| data.expire_time | long | 订单失效时间戳 10 位 |

### 状态码说明

| code | 描述 |
| :--- | :--- |
| 200 | 创建成功 |
| 4414 | 参数异常 |
| 4409 | 订单已存在 |
| 4403 | 签名不正确 |
| 4404 | 没有找到相关信息 |

### 请求例子

> 请求

```
curl -X POST -H 'Content-type: application/json' \
    -H 'ts: 1536200462' \
    -H 'appid: 00000000' \
    -H 'appkey: 1234567890123456' \
    -H 'sign: 74f9cca4418240b2340e36991d44e672' \
    -d '{"client_out_trade_no":"1540449058","channel":"wx", "platform":"h5","money":1,"client_ip":"127.0.0.1","notify_url":"https://yinqiantong.com/test","subject":"商品标题","description":"商品描述","extra":"this is extra param"}' \
    'https://api.yqtapi.com/order'
```

> 返回例子

```
{
  "msg": "创建成功",
  "code": 200,
  "data": {
    "out_trade_no": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "pay_body": "https://wx.tenpay.com/cgi-bin/mmpayweb-bin/checkmweb?prepay_id=wx25151618125264917b9fc0de4142376373&package=1479459154&redirect_url=",
    "expire_time": 1234567890
  }
}
```

### 相关问题

1. [什么是 `POST` 请求](http://www.w3school.com.cn/tags/html_ref_httpmethods.asp)

## 二，查询订单接口

[GET] `https://api.yqtapi.com/order?appid=00000000&client_out_trade_no=xxxxxxxxxxxxxxxxxxxx`

```
用于查询订单的详情信息
```

### url参数

| 参数 | 类型 | 是否必填 | 描述 |
| :---: | :---: | :---: | --- |
| appid | string | 必填 | **银钱通** App ID |
| client_out_trade_no | string | 必填 | 商户自定义订单号 |

### 返回参数

| 参数 | 类型  | 描述 |
| :--- | :---: | --- |
| code | int | 状态码，参考状态码说明 |
| msg | string | 提示信息 |

### 状态码说明

| code | 描述 |
| :--- | :--- |
| 200 | 支付成功 |
| 4201 | 等待支付 |
| 4400 | 支付失败 |
| 4408 | 订单已失效 |
| 4404 | 没有找到相关信息 |
| 4414 | 参数异常 |
| 4500 | 服务异常 |

### 请求例子

> 请求

```
curl 'https://api.yqtapi.com/order?client_out_trade_no=1540449058&appid=00000000' | jq
```

> 返回例子

```
{
  "msg": "支付成功",
  "code": 200
}
```

### 相关问题

1. [什么是 `GET` 请求](http://www.w3school.com.cn/tags/html_ref_httpmethods.asp)

## 三，支付回调通知

```
在用户支付成功后，我们根据您在创建订单时候的 notify_url 参数，会把支付结果，通过 `POST` 的方式，回调给商户。
商户需要有接受回调和处理回调结果的服务。
```

### 通知body

**Content-type: application/json**

| 参数 | 类型 | 描述 |
| :---: | :---: |  --- |
| appid | string | **银钱通** App ID ，请校验本地的appid是否一致 |
| ts | int | 当前时间戳，10位，请校验 `ts > 当前时间戳-86400秒` && `ts < 当前时间戳+86400秒` 条件是否成立 |
| sign | string | 接口签名，请校验签名，[签名方法](https://github.com/yinqiantong/docs/blob/master/doc/sign.md) |
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

### 处理结果

处理成功，返回 HTTP 状态 `200`。平台会认为非 `200` 的状态视为回调失败，回调会定时重试，每隔`1分钟`回调一次，重试超过`3次`，不再回调


## 错误码

| 错误码 | 描述 |
| :---: | :--- |
| 200 | 支付成功 |
| 4201 | 等待支付 |
| 4400 | 支付失败 |
| 4403 | 签名不正确 |
| 4404 | 没有找到相关信息 |
| 4408 | 订单已失效 |
| 4409 | 订单已存在 |
| 4414 | 参数异常 |
| 4500 | 服务异常 |
