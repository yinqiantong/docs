# 银钱通 签名

## 生成步骤

> 第一步

设所有发送或者接收到的数据为集合M，将集合M内非空参数值的参数按照参数名ASCII码从小到大排序（字典序），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串 `stringSignTemp`

特别注意以下重要规则：

* 参数名ASCII码从小到大排序（字典序）
* 如果参数的值为null或者空字符串不参与签名
* 参数名区分大小写

> 第二步

在`stringSignTemp`最后拼接上当前10位时间戳和`app_secret`得到`stringSignOrigin`字符串

> 第三步

对`stringSignOrigin`进行MD5运算，再将得到的字符串所有字符转换为小写，得到`sign`值

## 举例说明

假如传送的参数如下：

```
channel: wx
platform: app
money: 1
client_ip: 127.0.0.1
subject: 
body: 
```

假设当前时间戳为（此时间戳需要带上Header里面）： `1536053843`

假设app_secret为： `your_app_secret`

> 第一步，得到 `stringSignTemp`

```
stringSignTemp = "channel=wx&client_ip=127.0.0.1&money=1&platform=app"
```

> 第二步，得到 `stringSignOrigin`

```
stringSignOrigin = stringSignTemp + "&ts=1536053843&app_secret=your_app_secret"
```

> 第三部，得到签名

```
signTemp = md5(stringSignOrigin)
sign = signTemp.toLowerCase()

即：

md5("channel=wx&client_ip=127.0.0.1&money=1&platform=app&ts=1536053843&app_secret=your_app_secret").toLowerCase()
```

得出的签名如下

```
1917abc8b8908f9bef5cc86559c26edc
```

所以此请求的header为：

```
appid: your_app_id
appkey: your_app_key
ts: 1536053843
sign: 1917abc8b8908f9bef5cc86559c26edc
``` 

## 签名测试

[https://yinqiantong.com/html/experience/sign.html](https://yinqiantong.com/html/experience/sign.html)
