# 银钱通 签名

## 生成步骤

> 第一步

设所有发送或者接收到的数据为集合M，将集合M内非空参数值的参数按照参数名ASCII码从小到大排序（字典序），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串 `stringSignTemp`

特别注意以下重要规则：

* 参数名ASCII码从小到大排序（字典序）
* 如果参数的值为null或者空字符串不参与签名
* 参数名区分大小写
* 参数重需要带上 `appid` `ts`
* 集合M中不能包含 `sign` 信息，若有，请去掉此信息再签名

> 第二步

在`stringSignTemp`最后拼接上`app_secret`得到`stringSignOrigin`字符串

> 第三步

对`stringSignOrigin`进行MD5运算，再将得到的字符串所有字符转换为小写，得到`sign`值

## 举例说明

假如传送的参数如下：

```
channel: 1
platform: 2
money: 1
client_ip: 127.0.0.1
subject: my_subject
```

假设当前时间戳为： `1541051068`
假设appid为： `00000000`
假设app_secret为： `your_app_secret`

> 第一步，得到 `stringSignTemp`

```
stringSignTemp = "appid=00000000&channel=1&client_ip=127.0.0.1&money=1&platform=2&subject=my_subject&ts=1541051068"
```

> 第二步，得到 `stringSignOrigin`

```
stringSignOrigin = stringSignTemp + "&app_secret=your_app_secret"
```

此时 `stringSignOrigin` 的值为 `appid=00000000&channel=1&client_ip=127.0.0.1&money=1&platform=2&subject=my_subject&ts=1541051068&app_secret=your_app_secret`

> 第三部，得到签名

```
signTemp = md5(stringSignOrigin)
sign = signTemp.toLowerCase()

即：

md5("channel=wx&client_ip=127.0.0.1&money=1&platform=app&ts=1536053843&app_secret=your_app_secret").toLowerCase()
```

得出的签名如下

```
65d704b3652d451355f99b173635d107
```

## 签名测试

[https://yinqiantong.com/html/experience/sign.html](https://yinqiantong.com/html/experience/sign.html)
