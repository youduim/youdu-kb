# 有度集成开发场景

##### [集成指引](./developer/integration/summary)

将以企业的第三方应用向有度用户发送文本消息为例，介绍主动调用有度服务端API接口的使用办法。[查看详情](./developer/integration/summary)

##### [短信网关](./developer/integration/sms)

有度支持短信验证码登录以及在客户端向用户发送手机短信；缺省情况下，这些短信都是通过有度标准的短信网关服务来发送的。如果企业有自己的短信网关，也可以通过有度的接口进行二次开发，将有度短信服务集成到企业自己的短信网关上，这样有度使用的短信将通过企业自己的短信网关下发。[查看详情](./developer/integration/sms)

##### [内部客服](./developer/integration/ncs)

通过有度的企业应用消息回调接口，可以针对用户发送的消息进行自动化处理，类似消息机器人；也可以将不能自动处理的消息转发给特定的账号（客服），由该账号人工处理后进行回复。[查看详情](./developer/integration/ncs)



<!--通过有度的消息回调接口，将有度会话中的特定消息，转出并发送给第三方系统，由这些系统进行进一步跟踪处理；比如发给企业的任务管理系统产生一条新的任务，发给工单系统产生一条新的工单 等等。[查看详情](./developer/integration/msgprocess)-->
