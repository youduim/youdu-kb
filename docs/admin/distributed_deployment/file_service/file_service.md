# 文件服务独立部署说明文档

## 场景说明

　　详情请看[文件服务独立部署](admin/functions/filesvrdeploy)。

## 服务说明

　　本文档主要对文件服务独立部署操作进行详细说明；文件服务独立部署不仅仅只对下文的文件服务而言，而是指对涉及到的多个相关服务进行操作。

## 相关服务

| 内部服务进程   | 服务名称 | 描述                      |
| -------------- | -------- | ------------------------- |
| jgfiled.exe    | 文件服务 | 文件的上传和下载          |
| jgnetdiskd.exe | 网盘服务 | 存放网盘及群空间数据文件  |
| jgp2pd.exe     | P2P服务  | P2P文件传输               |
| jgupgraded.exe | 升级服务 | PC、Mac客户端升级配置服务 |

## 防火墙说明

　　为了保证文件服务独立部署之后各项内部服务能正常运行，需要对主服务器和文件服务器之间相关防火墙端口进行设置允许访问。

- 0.0.0.0:任何来源IP地址
- file_ip:文件服务器IP地址
- main_ip:有度主服务器IP地址

| 源地址  | 目标地址 |                                         | 备注                   | 可选端口设置       |
| ------- | -------- | --------------------------------------- | ---------------------- | ------------------ |
| 0.0.0.0 | file_ip  | 7080,7443                               | 客户端访问文件服务器   | 场景一             |
| 0.0.0.0 | file_ip  | 7081，7444                              | 客户端访问文件服务器   | 场景二，需修改端口 |
| main_ip | file_ip  | 7005,7039,7030,7017                     | 主服务器访问文件服务器 |                    |
| file_ip | main_ip  | 7020,7034,7026,7016,7001,7002,7010,7009 | 文件服务器访问主服务器 |                    |

!>上述客户端访问文件服务器的防火墙端口说明，需根据实际情况选择设置，只能二选一。



场景一：

1. 有度主服务器与文件服务器同属内网，而且仅内网使用有度时，不存在端口占用情况时，文件服务器使用默认端口。
2. 有度主服务器与文件服务器使用的外网出口IP不同，不存在端口占用情况，文件服务器使默认端口。

场景二：

　　有度主服务器与文件服务器同属内网，而且外网出口IP相同时，服务器之间存在外网使用端口冲突，所以文件服务器需修改默认端口，使用7081,7444（可根据实际可用端口设置）。

## 文件服务器安装有度服务端

　　详情请见[安装指南](admin/server_install/server_install)，安装完成后无需设置管理员、无需导入license。

## 有度主服务器配置步骤

### 停止主服务器相关内部服务

```
#编辑path/config/service.ini，把jgfile、jgp2p、jgnetdisk、jgupgrade的state值改为stop 
#例如：
 [jgfiled] 
 cmd = /bin/jgfiled.exe 
 state = stop
```



### 修改文件服务地址

```
#编辑path/config/svrlist.ini ，把jgfile、jgp2p、jgnetdisk、jgupgrade的info.host值改为文件服务器IP，并添加参数：info.virtual = true 
#例如：

[jgfiled] 
info.host    = file_ip 
info.virtual = true
```

### 修改客户端获取文件服务器的地址

```
根据实际情况修改DNS分组设置，以下是示例的sql语句：
#注意根据实际情况确认是否需要修改对应的groupname、IP、端口。 

update emoa_dns.t_service_addr set hostaddr='file_ip' where server in ('jgfile','jgp2p','ydnetdisk','jgupgrade');
```



## 文件服务器配置步骤

### 停止本机非文件相关服务

```
#编辑path/config/service.ini 
#保留jgdns、jgproxy、jgfile、jgp2p、jgnetdisk、jgupgrade的state值为start，其它为stop 
#例如：
[jgaccountd] 
 cmd = /bin/jgaccountd.exe 
 state = stop 
```

### 修改本机非文件相关服务地址

```
#编辑path/config/common.cfg.ini
#除了dnsaddr,其它服务的地址值改为主服务器IP
#例如：
msqaddr          = main_ip:7020

#编辑path/config/svrlist.ini
#把jgaccount、jgauth、jgmsg、jginfo、jgrca、jgstatisc的info.host值改为主服务器IP，并添加参数:info.virtual = true
#例如：
[jgaccountd]
info.host                 = main_ip
info.virtual = true
```



### 如果文件服务器与主服务器出口IP相同，主文件服务需修改客户端访问端口

```
#编辑path/config/jgproxy.ini
#将默认端口7080、7443分别改为7081、7444
#例如：
[7081]
xxxxxx
[7444]
xxxxxx
```

?>配置完成后，重启主服务器、文件服务器上的有度服务即可。