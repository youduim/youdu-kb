# 管理员



## 如何访问有度管理后台

-  打开浏览器，输入有度管理后台地址：http://服务器IP:7080/userportal

## 如何扩容授权用户数

- 在有度管理后台，点击【首页】左上角中的扩容按钮，填写相关信息后发送申请，会有专人联系。

![1584513884262](1584513884262.png)

## 如何添加多个有度后台管理员

有度管理后台支持添加多个管理员具体操作如下：

-  访问有度管理后台-【我的企业】-【权限管理】-【分级管理】-【超级管理员组】-【设置】，勾选组织架构中需要添加管理员权限的账号即可。

## 如何禁用有度账号登录

- 登录有度管理后台，在【通讯录】中存在授权名单和禁用名单，管理员可通过对授权名单和禁用名单进行账号的设置达到登录授权的划分的目的。

## 如何限制用户传输文件大小

- 登录有度管理后台，在【我的企业】-【聊天管理】-【文件传输限制】中。可对单个文件传输大小、P2P文件传输、互联企业文件传输进行限制，以达到限制用户传输文件大小目的。

## 如何配置客户端升级

- 登录有度管理后台，在【管理工具】-【客户端配置与升级】中，点击手动添加最新版本有度客户端进行配置升级；客户端在重新登录之后，便会收到服务端的新版本下载推送通知，升级使用即可。

![1584516704326](1584516704326.png)

## 有度服务端防火墙设置要求

安装完成后，如果服务器或者网络硬件设备有防火墙，需要映射如下端口： 

1) TCP 7080，服务器服务查询端口（**注意：客户端登录时填写的是这个端口**）； 

2) TCP 7006，服务器登录端口，实际对外提供登录服务； 

3) TCP 7443，后台服务均通过此代理端口对外提供服务； 

从 RTX 腾讯通升级到有度即时通的用户需要注意，如果是有度服务器和 RTX 服务器分 

开部署在两台服务器上的情况，则需要在额外开放一个 TCP 7014 端口，否则 RTX 和有度服务器之间将无法正常并行使用。 

为使推送服务正常运行，本服务器需要能访问以下地址、端口： 

1） TCP80、TCP443，youdu.im(有度官网)； 

2） TCP80、TCP443, api.youdu.im（有度推送通道）； 

3） TCP443，api.vmall.com（华为推送通道（接口））； 

4) TCP443，login.vmall.com（ 华为推送通道（验证））； 

5) TCP443，api.xmpush.xiaomi.com（小米推送通道）； 

6) TCP2195，gateway.push.apply.com（苹果推送通道）； 

7) TCP 2196，feedback.push.apply.com（苹果推送通道（反馈））；

## 如何设置防火墙入站规则

- Windows server 2008以上，win7以上

1. 打开控制面板——Windows防火墙，单击左侧的“高级设置”。

![1584519576222](1584519576222.png)

2.选中入站规则，右键单击“新建规则”。

3.单击“端口”，下一步。

4.输入7006,7080,7443。此处注意逗号为半角“,”

![1584519741001](1584519741001.png)

5.继续下一步。

6.输入规则名称，完成即可。

- lCentOS7系统

firewall-cmd --zone=public --add-port={7006,7080,7443}/tcp --permanent && firewall-cmd --reload && firewall-cmd --zone=public --list-ports



- CentOS 6、ubuntu16.04

  iptables -I INPUT -p tcp -m multiport --dports 7006,7080,7443 -j ACCEPT && service iptables save && service iptables restart



## 如何实现有度的第三方登录认证

1、在有度管理后台—企业应用—组织架构同步接口中，点击选用【通过调用接口同步】

2、在该页面中的URL里配置第三方认证服务的地址，该服务实现具体的认证过程，由第三方服务商提供

3、在有度后台的通讯录中，将需要第三方认证的人员的认证方式设置为【第三方认证】

4、程序DEMO文件下载地址：https://github.com/youduim/authdemo

5、[【配置说明文件】](https://teams.cindacode.com/download/attachments/1902991/有度第三方登录认证配置说明.docx?version=1&modificationDate=1560411171999&api=v2)

## 如何批量修改帐号的认证方式为第三方认证

如果仅修改单个、个别帐号为第三方认证，可以到管理后台直接对帐号进行修改。如果有批量修改需要，可以到数据库操作。

修改思路：设置帐号的authtype的值为2。

说明：0是有度本地认证，1是RTX认证，2是第三方认证（例如LDAP）

方法：

1.数据库查询语句（建议先查询确认下）：

SELECT account,chsname FROM emoa_acc.t_gid WHERE acctype=0 AND authtype=0 AND deleted=0 AND buin=总机号;

2.数据库更新语句：

update emoa_acc.t_gid set authtype=2 WHERE acctype=0 AND authtype=0 AND deleted=0 AND buin=总机号;

3,重启jgaccount进程生效。

# 故障问题

## 服务器断电导致MySQL服务无法启动，提示1067，如何恢复可用

原因分析：断电可能导致mysql数据损坏，数据损坏后将影响服务不可启动，所以不可用。

解决思路：尝试恢复mysql数据使用，如果恢复失败则恢复backup数据使用，并告知用户仅可包含组织架构、群、会话列表。

解决方法：

1.停止有度服务；

2.编辑有度配置文件Youdu/server/config/service.ini，将jgmfront的start改为stop,保存退出；停止jgmfront的作用是防止客户端连上服务器，在这种异常情况下可能出现其它问题。虽然第一步停止了有度服务，但是可能误操作启动或为后面重装服务端做准备；

3.使用innodb_force_recovery参数尝试启动有度服务

innodb_force_recovery = 1

**数值强烈建议从1逐步提升为6，不要直接设置为6。如果提升为6时仍然无法启动mysql服务，说明mysql数据彻底损坏。**不同级别的说明可以参考 https://www.cnblogs.com/glon/p/6728380.html

4.mysql服务启动成功后，马上使用mysqldump将数据全部备份

mysqldump -uroot p - all-databases -v > backup.sql

5.停止mysql服务；

6.重命名Youdu/Server下的ydmysql文件夹；

7.准备相同版本的服务端安装包，重装服务端；

8.停止有度服务；

9.恢复mysql数据；

10.编辑有度配置文件youdu/server/config/service.ini，将jgmfront的stop改为start,保存退出。

11.故障恢复完成，登录客户端检查功能；

注意：如果用户多次出现断电mysql数据损坏的情况，建议用户将有度服务端迁移至一个稳定的服务器环境。
