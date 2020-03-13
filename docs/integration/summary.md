# 有度集成指引

本文将以企业的第三方应用向有度用户发送文本消息为例，介绍主动调用有度服务端API接口的使用办法。

发送应用消息接口：POST请求URL：http://[SERVER:7080]/cgi/msg/send?accessToken=$acess_token。

[查看发送应用消息的开放接口详情](https://youdu.im/api/api.html#40041)

[有度开放平台](https://youdu.im/api/api.html#40011)

## 集成概述

有度服务端开放的接口，主要基于企业应用集成的模式，支持实现与企业的其他应用进行业务流程的整合、数据的交互等；集成中会使用企业总机号（buin）、企业应用ID（appid）进行身份验证，其中：

**企业总机号（buin）**：是有度为每个企业分配的唯一身份ID，打开有度的管理后台，在我的企业—企业信息—公司总机号 处可以获取该信息。

**企业应用ID（appid）**：是有度为每一个企业应用分配的唯一ID，在有度管理后台—企业应用，进入某个应用的详细页面，可以获取appid。

<u>**服务端接口均使用http协议、Json数据格式（包括返回的数据）；主动调用时均需传入accessToken进行认证，同时必须做数据加密处理，accessToken参数由企业总机号（buin）、企业应用ID（appid）换取**</u>。

## 集成流程

### 1.获取企业应用ID

在有度管理后台—企业应用页面，打开一个自建应用，在应用的主页面可以获取到appid及EncodingAESKey；如果还没有企业应用，可以通过【创建应用】添加一个即可。

### 2.换取accessToken

接口：POST请求URL：http://[SERVER:7080]/cgi/gettoken；[SERVER:7080]指连接的服务器地址。

请求的Json数据格式：

```
{
      "buin": $buin,   //企业总机号
      "appId": "$appId",  //企业应用ID
      "encrypt": "msg_encrypt"   //加密的消息
   }
```

返回的数据：

```
{
   "errcode": 0,
   "errmsg": "ok",
   "encrypt": "$encrypt_accessToken"
}
```

"$encrypt_accessToken"解密之后就是accessToken，Token有效期为7200秒（2小时）。

[查看获取accessToken的接口详情](https://youdu.im/api/api.html#40025)

[查看有度加解密办法](https://youdu.im/api/api.html#40024)

### 3.构造请求数据

主动调用有度服务端API接口，向有度用户发送文本消息时，使用Json格式来构造消息体msgJson：

```
{
     "toUser": "user1|user2|user3",
     "toDept": "deptId1|deptId2|deptId3",
     "msgType": "text",
     "text": {
        "content": "it is a text"
     }
}
```

参数说明：

| 参数    | 必须 | 说明                                                        |
| ------- | ---- | ----------------------------------------------------------- |
| toUser  | 否   | 接收成员帐号列表。多个接收者用竖线分隔，最多支持1000个      |
| toDept  | 否   | 接收部门id列表。多个接收者用竖线分隔，最多支持100个         |
| msgType | 是   | 消息类型，这里固定为：text                                  |
| content | 是   | 消息内容，支持表情，最长不超过600个字符，超出部分将自动截取 |

### 4.加密封装请求

String <u>encrypt</u> = AESEncode(<u>msgJson</u>)。

msgJson是第3步中构造的请求数据。

encrypt用于最终的接口调用时的参数传入。

[查看有度加解密办法](https://youdu.im/api/api.html#40024)

### 5.调用接口发送消息

POST请求URL：http://[SERVER:7080]/cgi/msg/send?accessToken=$acess_token。

$acess_token使用第2步换取的token传入。

post的Json数据格式：

```
{
      "buin": $buin,   //企业总机号
      "appId": "$appId",   //企业应用ID
      "encrypt": "encrypt"   // 第4步中加密封装的请求信息
   }
```

### 6.检查返回结果

```
{
   "errcode": 0,
   "errmsg": "ok"
}
如果发送失败，返回对应的errcode。如果接收者不存在，errmsg中会返回无效的user列表
```

[查看发送应用消息的接口详情](https://youdu.im/api/api.html#40041)

[有度开放平台](https://youdu.im/api/api.html#40011)