# FAQ常见问题

## 1.有度企业版（本地部署版）需要访问有度官网youdu.im的作用是什么？

在有度企业版的管理后台中，有访问有度开放平台的链接入口，该入口需要能访问有度官网。

## 2.有度客户端内置浏览器的缓存时间是多久，用户是否可以控制缓存时长

有度客户端目前没有加缓存；企业自己的WEB应用，可以在服务器返回的响应头中加入Expires策略或者Cache-Control策略来告诉客户端应该执行的缓存行为，同时配合#Last-Modified#等头来控制刷新的时机。

## 3.有度客户端发送消息到服务器的过程

1、客户端通过TCP(TLS Socket)通道登录成功，服务器返回用于https信息交互的token。

2、客户端将发送消息内容先进行base64，然后放进json协议包。

3、客户端将json协议包和token，通过https协议提交给服务器。

4、服务器验证token身份合法，将整个json数据包进行加密，把加密后的消息保存进leveldb或mongdb数据库。

## 4.有度与RTX并行，从RTX同步组织架构，但通过LDAP认证如何实现

1.在服务器配置文件svrlist.ini的jgaccount一节中,添加参数：private.rewrite_ldap_auth_type = true

2.重启jgaccount，在有度服务的进程中，直接杀掉该进程即可（会自动重启）

3.有度配置LDAP验证，但是不同步（关闭自动同步服务)。

## 5.有度客户端都支持哪些设备

| 设备编号 | 设备名称         | 设备编号 | 设备名称                 |
| -------- | ---------------- | -------- | ------------------------ |
| 0        | PC；各类台式电脑 | 6        | Web客户端（已停止支持）  |
| 1        | 苹果iphone手机   | 7        | 微信企业号（已停止支持） |
| 2        | 苹果ipad         | 9        | 华为手机                 |
| 3        | 各类android手机  | 10       | 小米手机                 |
| 4        | 各类android Pad  |          |                          |
| 5        | 苹果Mac电脑      |          |                          |

## 6.如何开启网络带宽限流

用户由于大群并发下载文件导致服务器带宽异常的高，内部其他服务出现500，无法响应；可以通过如下限流措施，可以防止带宽被占满而导致其他服务不可用的问题。

1、对接口请求数量进行限制

在有度服务器，打开jgproxy.ini配置文件，添加以下配置项：

[urllimit] 

/v3/api/jgfile/download = 100	//限制文件下载请求100个/s 

/v3/api/jgfile/download.original.image = 100 // 限制下载原图100个/s

2、对网络带宽进行限流

在jgproxy.ini配置文件中，common segment下添加：

netMonitorOn = 1   // 打开网络限流开关 

maxPermitNetUsage = 95  // 最大允许网络占用百分比 

netMonitorCheckDuration = 5  // 检测时间间隔,单位: 秒 

netBandwidth = 100  // 网络带宽：100, 单位: Mbps 

// 上述数据当前逻辑判断,五秒内产生的流量是否大于100Mb * 0.95,大于则限流禁用jgfile

## 7.如何重置客户端本地数据

当客户端发现一些异常时，比如消息发不出去、组织架构显示异常等等，可以通过重置客户端数据的方式来修复。

1、windows客户端

退出客户端，进入我的文档\youdu\个人文件夹（总机号+一串数字）\db目录，将session.db重命名（比如添加1），重新登录客户端。

2、Mac客户端

退出客户端，打开"/Users/电脑用户名/Library/Containers/im.xinda.youdu.mac/Data/Library/youdu/个人文件夹（总机号+一串数字）"，重命名或者删除session.db、msg.db，如果存在多个帐号，直接在youdu/下搜索session.db，msg.db，然后再重命名或者删除。

3、手机端直接卸载重装即可

上述重置动作完成后，客户端的消息会从服务端重新下载，不用担心消息丢失。

**如果上述操作还是不能解决问题，可以尝试将【我的文档\youdu\个人文件夹（总机号+一串数字）】整个删除，然后重新登录看看。**

## 8.如何修改有度对外网开放的端口

有度需要对外网开放7006、7080、7443三个端口；以下以Windows为例讲解对外端口的修改办法：

1、在有度服务器上，登录mysql（可以使用sqlyog等工具)。

2、执行以下语句：

use emoa_dns;

update t_service_addr set port=7006的新端口号 where port=7006;

update t_service_addr set port=7080的新端口号 where port=7080;

update t_service_addr set port=7443的新端口号 where port=7443;

3、修改配置文件

进入有度服务器Youdu\Server\config文件夹：

-修改svrlist.ini，

找到[jgmfrontd]，info.port = 7006的新端口号

-修改jgproxy.ini：

修改[:7080]为[:7080的新端口号]

修改[:7443]为[:7443的新端口号]

4、修改打开功能配置

修改Server/apps/clockapp/config.properties

加一个配置项，app.addr=127.0.0.1:7080的新端口

5、重启服务

在有度进程中，杀掉以下进程即可（会自动重启):

jgmfrontd.exe、jgproxyd.exe、jgclockapp.exe

## 9.如何通过有度服务端接口查询服务地址

1、查询总机号解析的DNS地址

http://dns.youdu.im/v3/api/jgdns/dns.list?buin=

2、查询DNS返回的所有服务地址

-如果jgdns在云免费版，使用这个地址查询（即云免费版、远程登录用户）：

http://dns.youdu.im/v3/api/jgdns/service.query?buin=

-本地部署版

http://localhost:7080/v3/api/jgdns/service.query?buin=0

-如需指定查询某个服务地址，URL后面续上：&server=服务名，例如查询jgfile：

http://localhost:7080/v3/api/jgdns/server.list?buin=0&server=jgfile

## 10.客户端文件收发失败，服务器返回503服务不可用错误

有度服务器有触发文件服务停止的机制，当服务器的CPU使用率达到95%以上，jgproxy会停止文件服务，所以请求会无法响应。可以通过以下操作进行关闭或调整：

打开有度服务端配置文件config/jgproxy.ini，在[common]节。

cpuMonitorOn：是否开启cpu监控。1：开启（默认），0：关闭

cpuMonitorCheckDuration：监控间隔。单位：秒，默认5秒。

maxPermitCpuUsage：cpu达到多少就限制对文件服务的访问，默认95（%）

就是说cpu使用率达到maxPermitCpuUsage值就开始对文件服务的请求进行限制(返回503错误信息)，然后等cpuMonitorCheckDuration秒后再次采集cpu数据，低于maxPermitCpuUsage值就不再限制，高于的话就继续限制，如此循环。

## 11.误删除账号该如何恢复

在有度管理后台删除某个账号，实际只打了一个删除标识，并没有物理删除，通过以下操作可以恢复该账号。

1、在有度服务器上，使用数据库工具（如SQLYOG）连接到MYSQL，在emoa_acc.t_gid表中找到该账号记录，修改字段deleted=0。

2、重启jgaccount（在有度服务器进程中杀掉该进程即可，会自动重启）

3、在有度管理后台的通讯录中搜索到这个用户，重新设置一下该用户的部门信息。

## 12.高可用方案部署时，LVS的VIP可以ping通，但是端口转发不通，该怎么解决

背景：高可用方案部署，selinux均已关闭、防火墙均已关闭、配置文件均检查无误。

VIP所属的网关IP与有度主备服务器的网关IP不是一个，可能受到影响，使用同一个网关的真实IP、虚拟IP可以解决此问题。另外VIP本机无法测通端口是正常的，需要在其它机器测试VIP的端口情况。

## 13.有度管理后台启用了打卡应用，在手机客户端中没有显示打卡应用，该怎么解决

1、有度管理后台-企业应用-应用模板管理中的默认模板里，看是否有添加打卡应用。

2、有度管理后台-启用应用页面，看看是否有新建“应用门户模板”，如果有，查看一下是否有添加打卡应用。

3、查看一下手机端-工作面板-设置，是否存在打卡应用可选添加项。

## 14.有度服务器CPU、内存占用高

有度服务器的CPU、内存占用高，可能与打开了消息回执有关；消息回执会处理大量的消息已读未读状态 ，从而导致CPU和内存占用高。尝试以下办法来解决问题：

1、在有度服务器进程中，停止jgmsg进程。

2、在有度服务安装路径下的db文件夹中，重新命名receiptdb文件（任意文件名都可以，系统会自动重新生成）。

3、在有度服务器进程中，重新启动jgmsg服务（杀掉进程即可，会自动重启）。

## 15.mysql报错提示1129，many connection

有度数据库报错，导致有度服务不可用；查看mysql日志，提示连接数过多many connection。通过以下办法解决：

1、登录mysql查看现在的连接数多大：show VARIABLES like 'max_connect%'

2、设置mysql连接数

set GLOBAL max_connects = 1000;

set GLOBAL max_connect_errors = 1000;

3、刷新主机连接活动：flush hosts;

4、如果使用了mysqlrouter,需要重启该服务。

## 16.有度客户端文件上传网盘失败

在有度服务器进程中，杀掉 jgminio、jgnetdisk两个进程即可（系统会自动重启）。

## 17.重装有度后服务启动不了，命令窗口提示指定的服务已标记为删除

1、升级有度服务，完成后管理后台登录不了，提示网页不存在。

2、检查发现安装youdu_mysql的时候提示“指定的服务”已标记为删除。

3、检查注册表项，将HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services下的mysql项目删除，重启服务器，重装服务，解决问题。

## 18、客户端登录无法获取会话、组织架构，日志报-2网络错误，但是服务器端正常

在客户端上，telnet服务器的三个端口7006、7080、7443，和上述报错一致。

在客户端上打开cmd命令行窗口，执行：netsh winsock reset，然后重启客户端电脑，问题可以解决。

如果一台机器上的Winsock协议配置有问题的话将会导致网络连接等问题，就需要用netsh winsock reset命令来重置Winsock目录借以恢复网络。

## 19.服务端4.0版本如何配置mongodb、leveldb信息

在有度服务器上，打开svrlist.ini,找到jgdata项，添加以下参数：

private.mongo_v4 = mongodb_ip:port

private.ldb_attach = 1

## 20.有度客户端搜索不到联系人

几种情况：

1、联系人实际存在，但搜不出来

2、联系人改过名字，但搜到的还是老的，组织架构显示的又是正确的

解决办法：在有度服务器进程中杀掉jgaccount进程（会自动重启）再试。

## 21.有度客户端内置浏览器用的什么内核

iOS、Mac端使用的是系统自带的webkit内核webview浏览器；Mac端部分情况会直接调用系统默认浏览器。

android客户端用的是系统自带webkit内核webview浏览器

Windows客户端用的Webkit内核webview浏览器