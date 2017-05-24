# dotEngine backend rest api


- dotEngine rest api 通过jwt来进行认证


### 创建token


URL

> `https://janus.dot.cc/api/createToken `

Method

> POST


Params

```
{
    'appkey':appkey,
    'sign':sign
}
```


得到sign的方法

```

准备params 如下

params = {
    'room':str,
    'user':str,
    'appkey':str, // 用户申请的appkey
    'expires':int, // 过期时间  默认  3600*24
    'role':str,  // 预留字段, 可以不设置
    'nonce':int,  // 随机数  nonce = random.randint(0,9999999)
}


sign  = jwt.encode(params, appSecret)

其中appSecret 为dotEngine为用户生成的APPSECRET

```


