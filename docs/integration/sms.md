# 短信网关

有度客户端支持短信验证码登录，同时支持向其他人发送手机短信；缺省情况下，这些短信都是通过信达公司的短信服务通道下发的。如果企业客户有自己的短信网关，有度提供了一套接口，支持企业客户进行二次开放，将有度的短信功能集成到自己的短信网关上，所有短信都通过自己的短信网关下发，不再走信达的短信通道。

短信网关的集成，可以通过有度的短信网关Demo来实现，集成开发流程如下：

## 1.导入开发工程

##### 1.1.下载有度短信网关DEMO

Demo支持Java开发语言，包含了短信发送代码及SDK包；下载地址：https://github.com/youduim/sms-demo-java

##### 1.2.导入pom.xml

Demo包含Src、pom.xml、ydskd.jar三部分内容，在java开发工具中，将pom.xml导入到工程内：

![导入pom](../\img\导入pom.png)

导入的时候，相关配置项需要打开，如下图：

![导入pomset](../\img\导入pomset.png)

##### 1.3.设置resource目录为资源文件夹

将 \gatewayDemo\src\main\resource 文件夹设置为资源文件夹；该文件夹下的SmsDemo.properties配置文件用于配置短信网关的应用参数。

##### 1.4.配置Artifacts

###### 1.4.1.删除原来所有的war包

![导入pomset](../\img\art-delwar.png)

###### 1.4.2.添加新Artifacts，点击“+”-> Web Application exploder -> from modules -> 确定，此时会出现下图，点击ok即可

![导入pomset](../\img\addart.png)

## 2.配置Tomcat

##### 2.1.进入Java开发工具的Tomcat配置界面

##### 2.2.点击“+”-> Tomcat Server -> local，一开始会提醒缺少artifacts

![导入pomset](../\img\addtomcat1.png)

##### 2.3.添加artifacts，选择后缀为exploded的artifacts->Apply->OK

![导入pomset](../\img\addtomcat2.png)

## 3.配置应用参数

调用有度的短信服务之前，需要配置相关参数；打开配置文件：\gatewayDemo\src\main\resource\SmsDemo.properties，配置短信网关应用参数：

> buin=14470142	
>
> //总机号，有度为每个企业分配的唯一ID，从【有度管理后台—我的企业—企业信息—公司总机号】取值
>
> srvHost=localhost:7080	
>
> //配置有度服务器地址
>
> appId=sysSmsAssistant	
>
> //应用ID，从【有度管理后台—管理工具—短信网关—appid】取值
>
> encodingAESKey=jLLs7czdB+5+qiksza3Iu9YlqhQ3W44bQ/awO2uLgtw=
>
> //编码key，从【有度管理后台—管理工具—短信网关—EncodingAESKey】取值

## 4.配置回调URL

配置回调URL ，用来指定企业自己的短信网关服务地址；有度需要发送短信时，会将请求发送给回调URL，由企业自己的短信网关完成短信的发送。

配置入口：有度管理后台—管理工具—短信网关—短信网关地址（HTTP服务地址），将企业短信网关的服务地址填入即可。

![导入pomset](../\img\smshttp.png)

## 5.设置短信权限

经过授权的有度用户才能向外发送短信；授权入口：有度管理后台—我的企业—权限管理—短信权限。

![导入pomset](../\img\smsright.png)

## 6.修改短信发送代码

## 7.客户端测试



