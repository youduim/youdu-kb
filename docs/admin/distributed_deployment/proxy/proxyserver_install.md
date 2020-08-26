# 代理服务器部署

## 场景说明

　当有度服务服务器部署在内网环境，而且不能直接暴露在外网环境中时；那么为了保证外网正常使用有度，如果单位有DMZ区（边界网络），就可以在DMZ区部署代理服务器，并将代理服务器映射到外网，外网有度客户端通过代理服务器登录使用。

## 服务器配置说明

| CPU      | 内存   | 硬盘    | 网络                           | 操作系统                                              |
| -------- | ------ | ------- | ------------------------------ | ----------------------------------------------------- |
| 双核以上 | 4G以上 | 50G以上 | 内网1000Mbs以上；外网10Mbs以上 | Windows server 2008 R2 以上；Linux发行版内核2.6以上； |

## 防火墙说明

- 0.0.0.0：所有来源地址

- proxy_ip:代理服务器地址

- main_ip:有度主服务器


| 源地址   | 目标地址 | 端口                                                   | 备注                   |
| -------- | -------- | ------------------------------------------------------ | ---------------------- |
| 0.0.0.0  | proxy_ip | 7006,7080,7443                                         | 客户端访问代理服务器   |
| proxy_ip | main_ip  | 7080,7443,7020,7034,7026,7016.7001,7002,7009,7010,7015 | 代理服务器访问主服务器 |

> 注意：此处防火墙仅作参考，如果有实际改动，请以实际为准。

## 部署步骤

### 1. 部署有度服务端

　　详情请参考：[有度服务端部署](admin/server_install/server_install)。

### 2. 停止本机非代理相关服务

```
#编辑path/config/service.ini #保留jgproxy的state值为start，其它为stop 
 #例如：
 
[jgaccountd]
cmd = /bin/jgaccountd.exe
state = stop 
```

### 3. 修改本机非代理相关服务地址

```
#编辑path/config/common.cfg.ini
#除了dnsaddr,其它服务的地址值改为主服务器IP
#例如：

[default]
dnsaddr          = 127.0.0.1:7004
msqaddr          = main_ip:7020
rcaaddr          = main_ip:7034
statis_addr      = main_ip:7026
statis_addr_http = main_ip:7016
tls.cn           = jgtest.cindasoft.com
tls.verify       = false
vercode          = 19210023
```

```
#编辑path/config/svrlist.ini
#把jgaccount、jgauth、jginfo、jgmsg、jgrca、jgstate、jgstatisc的info.host值改为主服务器IP，并添加参数info.virtual = true
#例如：

[jgaccountd]
info.host          = main_ip
info.virtual = true
```

### 

### 4. 修改代理地址，并保存退出。

```
#编辑path/config/jgproxy.ini
#新增以下配置

[:7006]
addr   = 0.0.0.0:7006
cert   = ../config/youdu.pem.crt
key    = ../config/youdu.key
tcpnat = tls:main_ip:7006

#修改7080、7443的所有host为主服务器IP，端口改为对应的7080、7443，7443的http协议改为https。
#例如：

[:7080]
rules(/cgi/).host                  = main_ip:7080
rules(/cgi/).location              = /cgi/
rules(/cgi/).path                  = /v3/api/jgapp/
rules(/cgi/).scheme                = http
rules(/clockapp/).host             = main_ip:7080
rules(/clockapp/).location         = /clockapp
rules(/clockapp/).scheme           = http
rules(/receive/tcexmail/).host     = main_ip:7080
rules(/receive/tcexmail/).location = /receive/tcexmail/
rules(/receive/tcexmail/).path     = ""
rules(/receive/tcexmail/).scheme   = http
rules(/upgrade/).host              = main_ip:7080
rules(/upgrade/).location          = /upgrade/
rules(/upgrade/).path              = ""
rules(/upgrade/).scheme            = http
rules(/userportal).host            = main_ip:7080
rules(/userportal).location        = /userportal
rules(/userportal).path            = ""
rules(/userportal).scheme          = http
rules(/v3/api/jgapp/).host         = main_ip:7080
rules(/v3/api/jgapp/).location     = /v3/api/jgapp/
rules(/v3/api/jgapp/).path         = ""
rules(/v3/api/jgapp/).scheme       = http
rules(/v3/api/jgdns/).host         = main_ip:7080
rules(/v3/api/jgdns/).location     = /v3/api/jgdns/
rules(/v3/api/jgdns/).path         = ""
rules(/v3/api/jgdns/).scheme       = http
rules(/v3/api/jgupgrade).host      = main_ip:7080
rules(/v3/api/jgupgrade).location  = /v3/api/jgupgrade
rules(/v3/api/jgupgrade).path      = ""
rules(/v3/api/jgupgrade).scheme    = http

[:7443]
admin                               = true
cert                                = ../config/youdu.pem.crt
key                                 = ../config/youdu.key
rules(/clockapp/).host              = main_ip:7443
rules(/clockapp/).location          = /clockapp
rules(/clockapp/).scheme            = https
rules(/connect).host                = main_ip:7443
rules(/connect).location            = /connect
rules(/connect).path                = ""
rules(/connect).scheme              = wss
rules(/jgview).host                 = main_ip:7443
rules(/jgview).location             = /jgview
rules(/jgview).path                 = ""
rules(/jgview).scheme               = https
rules(/mac_connect).host            = main_ip:7443
rules(/mac_connect).location        = /mac_connect
rules(/mac_connect).path            = ""
rules(/mac_connect).scheme          = wss
rules(/macchat/).host               = main_ip:7443
rules(/macchat/).location           = /macchat/
rules(/macchat/).path               = /
rules(/macchat/).scheme             = https
rules(/receive/tcexmail/).host      = main_ip:7443
rules(/receive/tcexmail/).location  = /receive/tcexmail/
rules(/receive/tcexmail/).path      = ""
rules(/receive/tcexmail/).scheme    = https
rules(/userportal).host             = main_ip:7443
rules(/userportal).location         = /userportal
rules(/userportal).path             = ""
rules(/userportal).scheme           = https
rules(/v3/api/jgapp/).host          = main_ip:7443
rules(/v3/api/jgapp/).location      = /v3/api/jgapp/
rules(/v3/api/jgapp/).path          = ""
rules(/v3/api/jgapp/).scheme        = https
rules(/v3/api/jgfile/).host         = main_ip:7443
rules(/v3/api/jgfile/).location     = /v3/api/jgfile/
rules(/v3/api/jgfile/).path         = ""
rules(/v3/api/jgfile/).scheme       = https
rules(/v3/api/jginfo/).host         = main_ip:7443
rules(/v3/api/jginfo/).location     = /v3/api/jginfo/
rules(/v3/api/jginfo/).path         = ""
rules(/v3/api/jginfo/).scheme       = https
rules(/v3/api/jgmsg/).host          = main_ip:7443
rules(/v3/api/jgmsg/).location      = /v3/api/jgmsg/
rules(/v3/api/jgmsg/).path          = ""
rules(/v3/api/jgmsg/).scheme        = https
rules(/v3/api/jgorg/).host          = main_ip:7443
rules(/v3/api/jgorg/).location      = /v3/api/jgorg/
rules(/v3/api/jgorg/).path          = ""
rules(/v3/api/jgorg/).scheme        = https
rules(/v3/api/jgp2p/ws).host        = main_ip:7443
rules(/v3/api/jgp2p/ws).location    = /v3/api/jgp2p/ws
rules(/v3/api/jgp2p/ws).path        = ""
rules(/v3/api/jgp2p/ws).scheme      = wss
rules(/v3/api/jgpush/).host         = main_ip:7443
rules(/v3/api/jgpush/).location     = /v3/api/jgpush/
rules(/v3/api/jgpush/).path         = ""
rules(/v3/api/jgpush/).scheme       = https
rules(/v3/api/jgqrcode/ws).host     = main_ip:7443
rules(/v3/api/jgqrcode/ws).location = /v3/api/jgqrcode/ws
rules(/v3/api/jgqrcode/ws).path     = ""
rules(/v3/api/jgqrcode/ws).scheme   = wss
rules(/v3/api/jgrca/).host          = main_ip:7443
rules(/v3/api/jgrca/).location      = /v3/api/jgrca/
rules(/v3/api/jgrca/).path          = ""
rules(/v3/api/jgrca/).scheme        = https
rules(/v3/api/jgstatisc/).host      = main_ip:7443
rules(/v3/api/jgstatisc/).location  = /v3/api/jgstatisc/
rules(/v3/api/jgstatisc/).path      = ""
rules(/v3/api/jgstatisc/).scheme    = https
rules(/v3/api/jgview/).host         = main_ip:7443
rules(/v3/api/jgview/).location     = /v3/api/jgview/
rules(/v3/api/jgview/).path         = ""
rules(/v3/api/jgview/).scheme       = https
rules(/v3/api/jgwfront/).host       = main_ip:7443
rules(/v3/api/jgwfront/).location   = /v3/api/jgwfront/
rules(/v3/api/jgwfront/).path       = ""
rules(/v3/api/jgwfront/).scheme     = https
rules(/v3/api/ydnetdisk/).host      = main_ip:7443
rules(/v3/api/ydnetdisk/).location  = /v3/api/ydnetdisk/
rules(/v3/api/ydnetdisk/).path      = ""
rules(/v3/api/ydnetdisk/).scheme    = https
rules(/webchat/).host               = main_ip:7443
rules(/webchat/).location           = /webchat/
rules(/webchat/).path               = /
rules(/webchat/).scheme             = https
```

### 5. 启动代理服务器有度服务。

### 6. 外网端口映射至代理服务，客户端测试登录。

### 7. 完成登录服务代理服务器部署。