# API 文档

**目录**

* [签名](#签名)
* [创建订单接口](#创建订单接口)
* [查询订单接口](#查询订单接口)
* [支付回调接口](#支付回调接口)

## 签名

* [签名说明](https://github.com/yinqiantong/docs/blob/master/doc/sign.md)
* [签名测试](https://yinqiantong.com/html/experience/sign.html)

## 创建订单接口

[POST] `https://yinqiantong.com/order`

### header 参数

| 参数 | 类型 | 是否必填 | 描述 |
| :---: | :---: | :---: | --- |
| appid | string | 必填 | **银钱通** App ID |
| appkey | string | 必填 | **银钱通** App Key |
| ts | int | 必填 | 当前时间戳，10位，此时间戳跟 **银钱通** 服务器的时间差不能超过 86400 |
| sign | string | 必填 | 签名。[签名方法](https://github.com/yinqiantong/docs/blob/master/doc/sign.md) |

### body 参数

| 参数 | 类型 | 是否必填 | 描述 |
| :---: | :---: | :---: | --- |
| channel | string | 必填 | 支付渠道（wx：微信；alipay：支付宝） |
| platform | string | 必填 | 支付平台（app：手机支付；mp：公众号支付；mini_app：小程序支付；h5：H5支付；pc：PC网页支付） |
| money | int | 必填 | 金额。以`分`为单位，1元=100分。money>0 |
| client_ip | string | 必填 | 发起支付请求客户端的 IPv4 地址，如: 127.0.0.1 |
| notify_url | string | 必填 | 回调地址，用户支付成功后，我们服务器会主动发送一个post消息到这个网址 |
| open_id | string | 某些条件下必填 | open id，微信公众号/微信小程序必填此参数 |
| subject | string | | 商品标题 |
| description | string | | 商品描述 |
| extra | string | | 额外参数，支付成功后，原样返回 |

### 返回参数

| 参数 | 类型  | 描述 |
| :--- | :---: | --- |
| code | int | 状态码 |
| msg | string | 提示信息 |
| data | json object |  |
| data.app_id | string | **银钱通** 的 App ID |
| data.channel | string | 支付渠道（wx：微信；alipay：支付宝） |
| data.platform | string | 支付平台（app：手机支付；mp：公众号支付；mini_app：小程序支付；h5：H5支付；pc：PC网页支付） |
| data.out_trade_no | string | 全局唯一的订单号 |
| data.status | string | 订单状态，unpaid:未支付；paid:支付成功；expired:已失效；refunding:正在退款；refunded:已退款 |
| data.money | int | 订单金额。以`分`为单位，1元=100分。money>0 |
| data.client_ip | string | 发起支付请求客户端的 IPv4 地址，如: 127.0.0.1 |
| data.open_id | string | open id |
| data.subject | string | 商品标题 |
| data.description | string | 商品描述 |
| data.extra | string | 额外参数 |
| data.pay_body | string | 支付相关的请求内容，转成字符串 |
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
    -d '{"channel":"alipay", "platform":"h5","money":1,"client_ip":"127.0.0.1","subject":""}' \
    'https://yinqiantong.com/order'
```

> 返回例子

```
{
    "msg": "ok", 
    "code": 200, 
    "data": {
        "app_id": "00000000", 
        "channel": 1, 
        "platform": 3, 
        "out_trade_no": "0000000013153621399830c1a3e", 
        "status": 0, 
        "money": 1, 
        "client_ip": "127.0.0.1", 
        "open_id": "", 
        "subject": "", 
        "description": "", 
        "notify_url": "", 
        "success_url": "", 
        "extra": "", 
        "pay_body": "<form id='alipaysubmit' name='alipaysubmit' action='https://openapi.alipay.com/gateway.do?charset=utf-8' method='post'><input type='hidden' name='app_id' value='2018081661080671'/><input type='hidden' name='method' value='alipay.trade.wap.pay'/><input type='hidden' name='format' value='JSON'/><input type='hidden' name='charset' value='utf-8'/><input type='hidden' name='sign_type' value='RSA2'/><input type='hidden' name='version' value='1.0'/><input type='hidden' name='return_url' value=''/><input type='hidden' name='notify_url' value='http://yinqiantong.com/alipay/notify'/><input type='hidden' name='timestamp' value='2018-09-06 14:06:38'/><input type='hidden' name='sign' value='tQ9pnnTWf/8lwbCJTBS0lCHYAbDvpLXeTme7Zg3FXyb7UXiS1q71pBSi8FCSH0KaDY+Jj/JngixkjLry9idvl8gYBa0fsgE4vrf2XBid55zE0sFDZDVEtE5YqnyRhEBPGZPE3Rlr1lRiPZDxHp5Tixq9Pybb9kcttgaqBqk9WPsZRqDq35YTeEq+no5OVUJ2fzdQoKteHridRubduFtDgAmlPujduUK8RqpwTOGRy/F2+MnW9DWBvwfTc4ePxwZyfjk+56lotgcsdYlk50Gx11xjRVdWQLd6mfk32R7+UG+B/AD2i/dhCNJZc0lQaUyVVmOFB2KpOW2tpKPEGi3//g=='/><input type='hidden' name='biz_content' value='{\"out_trade_no\":\"0000000013153621399830c1a3e\",\"total_amount\":1,\"subject\":\"\",\"notify_url\":\"http:\\/\\/yinqiantong.com\\/api\\/alipay\\/notify\",\"product_code\":\"QUICK_WAP_WAY\"}'/><input type='submit' value='ok' style='display:none;'></form><script>document.forms['alipaysubmit'].submit();</script>", 
        "pay_time": 0, 
        "expire_time": 1536221198, 
        "create_time": 1536213998
    }
}
```

## 查询订单接口


## 支付回调接口
