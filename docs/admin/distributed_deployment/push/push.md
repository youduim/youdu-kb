# 消息推送服务独立部署

## 消息推送介绍

   详情请见功能逻辑中的[消息通知](admin/functions/消息通知)。

## 防火墙策略说明



| 源地址  | 目标地址                              | 端口                                         | 备注                   |
| ------- | ------------------------------------- | -------------------------------------------- | :--------------------- |
| main_ip | push_ip                               | 7012                                         | 主服务器访问推送服务器 |
| push_ip | main_ip                               | 7020,7034,7026,7016,7001,7002,7009,7010,7015 | 推送服务器访问主服务器 |
| push_ip | youdu.im, api.youdu.im,               | 80、443                                      | 有度推送通道           |
| push_ip | ver.youdu.im                          | 443                                          | PC客户端检测更新通道   |
| push_ip | api.push.hicloud.com, login.vmall.com | 443                                          | 华为推送通道           |
| push_ip | api.xmpush.xiaomi.com                 | 443                                          | 小米推送通道           |
| push_ip | server-api-push.meizu.com             | 80                                           | 魅族推送通道           |
| push_ip | gateway.push.apple.com                | 2195                                         | 苹果推送通道           |
| push_ip | feedback.push.apple.com               | 2196                                         | 苹果推送通道（反馈）   |

## 推送服务器安装有度服务端

 　　详情请见[安装指南](admin/server_install/server_install)，安装完成后无需设置管理员、无需导入license。

## 主服务器配置

1. 停止本机推送服务

```
#编辑path/config/service.ini
#把jgpush的state值改为stop
#例如：
[jgpushd] cmd = /bin/jgpushd.exe
state = stop
```

2. 修改推送服务地址

```
#编辑path/config/svrlist.ini
#把jgpush的info.host值改为推送服务器IP，并添加参数info.virtual = true
#例如： 
[jgpushd] 
info.host = push_ip 
info.virtual = true
```

3. 修改客户端获取文件服务器的地址

```
#注意根据实际情况确认是否需要修改对应的groupname、IP、端口。
以下是示例的sql语句：

#修改内网推送服务地址（即default组）
update emoa_dns.t_service_addr set hostaddr='push_ip' where server='jgpush';

#主服务器和推送服务器使用同一个出口IP。同一个出口IP因为端口的唯一性，因此文推送服务需修改客户端访问端口（即jgproxy的监听端口）。

#插入分组outer
insert  into emoa_dns.`t_service_group`(`name`,`description`) values ('outer','');

#插入分组匹配的目标地址
insert  into emoa_dns.`t_service_entgroup`(`buin`,`groupName`,`domain`,`remote`) values (0,'outer','outer_ip','');

#插入分组outer的有度服务
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgmfront', '',7006,'tcp');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgrtxagent','', 7014,'tcp');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgqrcode','', 7443,'wss');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgfile','', 7443,'https');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgmsg','', 7443,'https');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgorg','', 7443,'https');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgpush','', 7443,'https');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jginfo','', 7443,'https');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgupgrade','', 7080,'http');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jguserportal','', 7080,'http');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgapp','', 7443,'https');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','jgp2p','', 7443,'wss');
INSERT ignore INTO `t_service_addr` VALUES (null,'outer','ydnetdisk','', 7443,'https');

#修改推送服务端口
update emoa_dns.t_service_addr set port=7444 where server='jgpush';
```

## **推送服务器**

1. 停止本机非推送相关服务

```
#编辑path/config/service.ini 
#保留jgdns、jgpush的state值为start，其它为stop 
#例如： 
[jgaccountd] 
cmd = /bin/jgaccountd.exe 
state = stop
```

2. 修改本机非推送相关服务地址

```
#编辑path/config/common.cfg.ini 
#除了dnsaddr,其它服务的地址值改为主服务器IP 
#例如： 
msqaddr = main_ip:7020 

#编辑path/config/svrlist.ini 
#把jgaccount、jgauth、jginfo、jgmsg、jgrca、jgstate、jgstatisc的info.host值改为主服务器IP，并添加参数info.virtual = true 
#例如： 
[jgaccountd] 
info.host = main_ip 
info.virtual = true
```

3. 如果推送服务器与主服务器出口IP相同，推送服务需修改客户端访问端口

```
#编辑path/config/jgproxy.ini

#将默认端口7080、7443分别改为7081、7444
#例如：
[7081]
xxxxxx
[7444]
xxxxxx
```



?>主服务器、推送服务器配置完毕后，重启主服务器、推送服务器有度服务即可。