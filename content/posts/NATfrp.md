---
title: "基于frp内网穿透"
date: 2022-11-08T15:57:49+08:00
draft: false
tags: ["net"]
---

转载自[https://sspai.com/post/52523](https://sspai.com/post/52523)，如有侵权请联系删除

<!--more-->

# Why

**为了从公网中访问自己的私有设备**<br>
&emsp;&emsp;自己的主力台式机、NAS等设备(虽然我现在都没有)，他们大概率都处于路由器后，由于运营商不给分配公网IP而不能直接访问(远程桌面，远程文件，SSH等)，一般要通过一些转发或者P2P组网软件的帮助。<br>
&emsp;&emsp;现在有一台计算机位于一个很复杂的局域网中，我想要实现远程桌面和文件访问，目前来看其所处的网络环境很难通过简单的端口映射将其暴露在公网之中，解决办法其三如下：

1. 远程桌面使用TeamViewer。可用，但需要访问端也拥有TeamViewer软件，不是很方便，希望能使用Windows自带的远程桌面。且TeamViewer不易实现远程文件访问。
2. 使用蒲公英VPN软件进行组网，或者使用花生壳软件进行DDNS解析，可用，但免费版本网络速度极慢，体验不佳，几乎无法正常使用。
3. **搭建frp服务器进行内网穿透，可用且推荐，可以达到不错的速度，且理论上可以开放任何想要的端口，可以实现的功能远不止远程桌面或者文件共享。**

# 什么是frp

&emsp;&emsp;简单地说，[frp](https://github.com/fatedier/frp/blob/master/README_zh.md)就是一个[反向代理软件](https://www.zhihu.com/question/24723688)，它体积轻量但功能很强大，可以使处于内网或防火墙后的设备对外界提供服务，它支持HTTP、TCP、UDP等众多协议。我们今天仅讨论TCP和UDP相关的内容。<br>
目前frp最新版本v0.45.0

# 先前条件

1. VPS/具有公网IP的实体机
2. 要访问的目标设备
3. Linux几个基础命令

## VPS相关

+ 因为frp的原理是利用服务端（所准备的VPS）进行转发，因而VPS的速度直接决定了之后连接的质量，请根据自己的需要选择相应主机配置。
+ 我现在用的国内的，需要备案，比较麻烦，也可以用国外的，但是注意网络质量，可以买挑选几台，先买一个月测试一下网速
+ 系统使用ubuntu就行，不过frp也支持windows，无所谓操作系统（服务端似乎一般都是Linux系统）

# 服务端设置

ssh到vps之后先查看处理器架构，根据架构下载不同版本的frp

`arch`

查看结果，如果是"X86_64"即可选择"amd64"

去github上找到[最新版本的frp下载链接](https://github.com/fatedier/frp/releases)进行下载

`wget [url]`

然后解压

`tar -zxvf frp_0.45.0_linux_amd64.tar.gz`

文件夹改个名字

`mv frp_0.45.0_linux_amd64 frp`

把解压出来的文件夹复制到你想要的目录下，进入该目录

`cd frp`

查看一下文件

`ls -al`

我们只需要关注如下几个文件
+ frps
+ frps.ini
+ frpc
+ frpc.ini

前两个文件（s结尾代表server）分别是服务端程序和服务端配置文件，后两个文件（c结尾代表client）分别是客户端程序和客户端配置文件。

因为我们正在配置服务端，可以删除客户端的两个文件

`rm frpc`

`rm frpc.ini`

然后修改frps.ini文件

`vim frps.ini`

修改为类似如下

```ini
[common]
bind_port = 7000
dashboard_port = 7500
token = 12345678
dashboard_user = admin
dashboard_pwd = admin
# vhost_http_port = 10080
# vhost_https_port = 10443
```

> 如果没有必要，端口均可使用默认值，token、user和password项请自行设置。

+ “bind_port”表示用于客户端和服务端连接的端口，这个端口号我们之后在配置客户端的时候要用到。
+ “dashboard_port”是服务端仪表板的端口，若使用7500端口，在配置完成服务启动后可以通过浏览器访问 x.x.x.x:7500 （其中x.x.x.x为VPS的IP）查看frp服务运行信息。
+ “token”是用于客户端和服务端连接的口令，请自行设置并记录，稍后会用到。
+ “dashboard_user”和“dashboard_pwd”表示打开仪表板页面登录的用户名和密码，自行设置即可。
+ “vhost_http_port”和“vhost_https_port”用于反向代理HTTP主机时使用，本文不涉及HTTP协议，因而照抄或者删除这两条均可。

编辑完成保存之后我们就可以运行frps的服务端了

`./frps -c fprs.ini`

如果看到屏幕输出这样一段内容，即表示运行正常，如果出现错误提示，请检查上面的步骤。

```
2019/01/12 15:22:39 [I] [service.go:130] frps tcp listen on 0.0.0.0:7000
2019/01/12 15:22:39 [I] [service.go:172] http service listen on 0.0.0.0:10080
2019/01/12 15:22:39 [I] [service.go:193] https service listen on 0.0.0.0:10443
2019/01/12 15:22:39 [I] [service.go:216] Dashboard listen on 0.0.0.0:7500
2019/01/12 15:22:39 [I] [root.go:210] Start frps success
```

此时访问 x.x.x.x:7500 并使用自己设置的用户名密码登录，即可看到仪表板界面

<img src="https://tvax4.sinaimg.cn/large/007Z9xVHgy1h7xsz7fes0j31z414h7b8.jpg" alt="frp服务端仪表板界面" >

# 服务端后台运行

&emsp;&emsp;至此，我们的服务器端仅运行在前台，如果Ctrl+C停止或者关闭ssh窗口后，frps均会停止运行，因而我们使用[nohup命令](https://ehlxr.me/2017/01/18/Linux-%E7%9A%84-nohup-%E5%91%BD%E4%BB%A4%E7%9A%84%E7%94%A8%E6%B3%95/)将其运行在后台

`nohup ./frps -c frps.ini &`

&emsp;&emsp;输出如下内容即表示正常运行

```shell
nohup: ignoring input and appending output to 'nohup.out'
```

&emsp;&emsp;此时课先使用Ctrl+C关闭nohup，frps依然会在后台运行，使用jobs/ps/top/htop等命令查看后台运行的程序即可发现

此时访问 [ip]:7500 依然可以打开仪表盘界面，至此，服务端设置完成，可以关闭ssh窗口了

# 客户端设置

> frp的客户端一般是Windows系统，本文是用Windows系统举例，Linux系统配置方法类似

同样的，根据客户端设备选择相应的frp程序进行[下载](https://github.com/fatedier/frp/releases)<br>
假定你下载了“frp_0.45.0_windows_amd64.zip”，将其解压在了C判根目录下，并且将文件夹重命名为“frp”，可以删除其中的frps和frps.ini文件。<br>
打开frpc.ini，与服务端类似，内容如下

```ini
[common]
server_addr = x.x.x.x
server_port = 7000
token = won517574356
[rdp]
type = tcp
local_ip = 127.0.0.1           
local_port = 3389
remote_port = 7001  
[smb]
type = tcp
local_ip = 127.0.0.1
local_port = 445
remote_port = 7002
```

其中common字段下的三项纪委服务端的设置
+ “server_addr"为服务端ip地址
+ “server_port”为服务器端口，填入你设置的端口号即可，如果未改变就是7000
+ “token”是你在服务器上设置的连接口令

# 自定义规则

&emsp;&emsp;frp实际使用时，会按照端口号进行对应的转发，原理如下图所示

<img src="https://tvax4.sinaimg.cn/large/007Z9xVHgy1h82gd7a6cbj31ad0hx44q.jpg" alt="frp的原理" >

上面的frpc.ini的rdp、smb字段都是自己定义的队则，自定义端口对应时格式如下：

+ “[xxx]”