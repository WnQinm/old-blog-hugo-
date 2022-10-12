---
title: "Kali(docker)"
date: 2022-09-24T13:26:40+08:00
draft: false
tags: ["net","docker","hack"]
---

本篇是kali基础的学习笔记
<!--more-->

# 配置kali的apt在线安装包源

`vim /etc/apt/sources.list`

把原先的deb注释掉，然后添加：

```shell
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
```
然后`apt update`
其次通过下面的代码安装必要的工具

```shell
# 安装本体（只是一个空壳）
docker pull kalilinux/kali-rolling

# 这里面啥也无，先安装基本包
apt install kali-linux-core
# 二百多兆，常见的简单工具

# 常用工具都在这里，好几个G
apt install kali-linux-headless

# 越来越大的包...
kali-linux-large
kali-linux-everything

```

# 利用第三方服务对目标进行被动信息收集防止被发现

## 被动信息收集

### 被动信息收集概述和目的

信息收集的方式可以分为两种：被动和主动

被动信息收集方式是指利用第三方服务对目标进行访问了解，例如google

主动的信息收集方式：通过直接访问、扫描网站，这种将流量流经网站的行为。比如nmap扫描端口

被动信息收集的目的：通过公开渠道去获得目标主机的信息，从而不与目标系统直接交互，避免留下痕迹

### 信息收集的内容

1. ip地址段
2. 域名信息
3. 邮件地址
4. 文档图片数据
5. 公司地址
6. 公司组织架构
7. 联系电话/传真号码
8. 人员姓名/职务
9. 目标系统使用的技术架构
10. 公开的商业信息

### 信息用途

1. 信息描述目标
2. 发现目标
3. 社会工程学攻击
4. 物理缺口

## 信息收集-DNS

### 域名解析原理

**DNS服务器概述**：

运行DNS服务器程序的计算机，储存DNS数据库信息。

DNS服务器分为根域DNS服务器、顶级域名DNS服务器。根域DNS服务器有13个，都存储了全部的顶级域名服务器的所在地址；顶级域名服务器存储了每位客户所注册的主机地址，这里对应到 163.com. 这个级别。

**域名记录：A,Cname,NS,MX,PTR**

(1) A记录（Address）正向解析:
A记录是将一个主机名（全程域名FQDN）和一个IP地址关联起来。这也是大多数客户端程序默认的查询类型。例：wnqinm.cn->6.6.6.6

(2) PTR记录（Pointer）反向解析:
PTR记录将一个ip地址对应到主机名（全程域名FQDN）。这些记录保存在in-addr.arpa域中。

(3) CNAME记录（Canonical Name）别名：
别名记录，也成为规范名字（Canonical Name）。这种记录允许将多个名字映射到同一台计算机。例如：www.wnqinm.cn 对应 IP6.6.6.6，web.wnqinm.cn 对应IP 6.6.6.6

(4) MX记录（Mail eXchange）
MX记录是邮件交换记录，它指向一个邮件服务器，用于电子邮件系统发邮件时根据收信人的地址后缀来定位服务器。@163.com
当有多个MX记录（即有多个邮件服务器）时，则需要设置数值来确定其优先级。通过设置优先级数字来指明首选服务器，数字越小表示优先级越高。

(5) NS记录（Name Server）
NS记录时域名服务器记录，也成为授权服务器，用来指定该域名由哪个DNS服务器来进行解析

**DNS缓存服务器**

不负责解析域，只是缓存域名解析结果

DNS查询方式：递归查询和迭代查询

一个DNS查询过程，通过8个步骤的解析过程就使得客户端可以顺利访问 www.163.com 这个域名，但实际应用中，通常这个过程时非常迅速的，如图所示：

![dns](https://tvax4.sinaimg.cn/large/007Z9xVHgy1h6bux7kwytj30sl0jt40e.jpg)

### DNS信息收集-NSLOOKUP

1. **将域名解析为ip地址**

通过域名获得ip地址最快速的方式：ping

```shell
# -c [num] 指定发送的数据包个数

$ ping www.baidu.com -c 3
PING www.a.shifen.com (180.101.49.11) 56(84) bytes of data.
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=1 ttl=48 time=9.92 ms
64 bytes from 180.101.49.11 (180.101.49.11): icmp_seq=2 ttl=48 time=9.97 ms
64 bytes from 180.101.49.11: icmp_seq=3 ttl=48 time=9.91 ms

--- www.a.shifen.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 10168ms
rtt min/avg/max/mdev = 9.911/9.931/9.969/0.026 ms
```

2. 使用nslookup查看域名

```shell
$ nslookup www.baidu.com
Server:         114.114.114.114
Address:        114.114.114.114#53

Non-authoritative answer:
www.baidu.com   canonical name = www.a.shifen.com.
Name:   www.a.shifen.com
Address: 180.101.49.12
Name:   www.a.shifen.com
Address: 180.101.49.11
```

### DNS信息收集-DIG

```shell
# dig [参数] 要查询的域名
# @<DNS服务器地址>	：	指定进行域名解析的域名服务器
# any		显示所有类型的域名记录。默认只显示A记录

$ dig www.baidu.com
; <<>> DiG 9.18.6-2-Debian <<>> www.baidu.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 27865
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.baidu.com.                 IN      A

;; ANSWER SECTION:
www.baidu.com.          519     IN      CNAME   www.a.shifen.com.
www.a.shifen.com.       219     IN      A       180.101.49.11
www.a.shifen.com.       219     IN      A       180.101.49.12

;; Query time: 12 msec
;; SERVER: 114.114.114.114#53(114.114.114.114) (UDP)
;; WHEN: Sat Sep 24 15:56:38 UTC 2022
;; MSG SIZE  rcvd: 101

$ dig @8.8.8.8 www.baidu.com
; <<>> DiG 9.18.6-2-Debian <<>> @8.8.8.8 www.baidu.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 2814
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.baidu.com.                 IN      A

;; ANSWER SECTION:
www.baidu.com.          941     IN      CNAME   www.a.shifen.com.
www.a.shifen.com.       116     IN      A       180.101.49.11
www.a.shifen.com.       116     IN      A       180.101.49.12

;; Query time: 72 msec
;; SERVER: 8.8.8.8#53(8.8.8.8) (UDP)
;; WHEN: Sat Sep 24 15:56:53 UTC 2022
;; MSG SIZE  rcvd: 101

$ dig @8.8.8.8 www.baidu.com any
```

也可以通过ip来查询，需要在`dig`后加参数`-x` 

```shell
$ dig -x 114.114.114.114
; <<>> DiG 9.18.6-2-Debian <<>> -x 114.114.114.114
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32637
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;114.114.114.114.in-addr.arpa.  IN      PTR

;; ANSWER SECTION:
114.114.114.114.in-addr.arpa. 379 IN    PTR     public1.114dns.com.

;; Query time: 12 msec
;; SERVER: 114.114.114.114#53(114.114.114.114) (UDP)
;; WHEN: Sat Sep 24 16:02:42 UTC 2022
;; MSG SIZE  rcvd: 89
```

查询DNS服务器bind版本信息

目的：可以通过版本信息来查找相关版本漏洞的利用方式

```shell
$ dig txt chaos VERSION.BIND @ns3.dnsv4.com	# @后跟DNS服务器域名
;; Warning: query response not set
;; Warning: Message parser reports malformed message packet.

; <<>> DiG 9.18.6-2-Debian <<>> txt chaos VERSION.BIND @ns3.dnsv4.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 48413
;; flags: rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;VERSION.BIND.                  CH      TXT

;; ANSWER SECTION:
VERSION.BIND.           0       CH      TXT     "DNSPod AUTHORITY DNS 7.2.2203.01"	# 版本信息

;; Query time: 4 msec
;; SERVER: 61.151.180.49#53(ns3.dnsv4.com) (UDP)
;; WHEN: Sat Sep 24 16:10:08 UTC 2022
;; MSG SIZE  rcvd: 75
```

### 查询网站的域名注册信息和备案信息

1. 域名注册信息

   whois 域名   或   web接口查询

阿里云：www.whois.aliyun.com

站长之家：http://whois.chinaz.com/

2. 备案信息查询

+ web接口查询
  + https://website.informer.com/
  + http://icp.chinaz.com/
  + https://beian.miit.gov.cn/
  + https://tianyancha.com/

## 使用Maltego收集子域名信息

### 子域名介绍

顶级域名是域名的最后一个部分，即是域名最后一点之后的字母，例如在https://baidu.com这个域名中，顶级域是.com（或.COM），大小写视为相同

常见的顶级域主要分两类：

1. 通用顶级**类别域名**共6个，包括用于科研机构的.ac；用于工商金融企业的.com；用于教育机构的.edu；用于政府部门的.gov；用于互联网络信息中心和运行中心的.net；用于非盈利组织的.org
2. 国家及地区顶级域，如中国.cn，英国.uk，地理顶级域名一般由各个国家或地区负责管理

子域名（Subdomain Name），凡顶级域名前加前缀的都是该顶级域名的子域名，而子域名根据技术的多少分为二级子域名，三级子域名以及多级子域名

### 挖掘子域名的重要性

子域名是某个主域的二级域名或者多级域名，在防御措施严密的情况下无法直接拿下主域，那么就可以采用迂回战术拿下子域名，然后无限靠近主域

+ 子域名挖掘工具：Maltego子域名挖掘机
+ 搜索引擎挖掘 如：在Google中输入 site:qq.com
+ 第三方网站查询：https://tool.chinaz.com/subdomain、https://dnsdumpster.com/
+ 证书透明度公开日志枚举：https://crt.sh/、https://censys.io/
+ 其他途径：https://phpinfo.me/domain、https://dns.aizhan.com

### 使用Maltego CE进行子域名挖掘

注册账号地址：https://www.maltego.com/ce-registration/



## Shodan信息收集

### Shodan介绍

Shodan直接进入互联网背后的通道搜索信息，一刻不停的寻找着所有和互联网关联的服务器、摄像头、打印机、路由器等。还可以直接显示出目标的具体地理位置信息

https://www.shodan.io

# 主动信息收集

## 主动信息收集的原理

### 主动信息收集的特点

1. 直接与目标系统交互通信
2. 无法避免留下访问痕迹
3. 使用受控的第三方电脑进行探测，使用代理或已经被控制的机器，做好被封杀的准备
4. 扫描发送不同的探测，根据返回的结果判断目标状态

### 发现目标主机的过程

1. 识别存活主机，发现潜在的被攻击目标
2. 输出一个ip地址列表比如ip地址段  ip地址范围
3. 使用二、三、四层进行探测发现

### OSI七层模型和TCP/IP五层模型

![osi](https://tva4.sinaimg.cn/large/007Z9xVHgy1h6i7pfmq8rj30ra0esgu4.jpg)

![tcp_ip](https://tva1.sinaimg.cn/large/007Z9xVHgy1h6i7vxjszqj30ku0dr77q.jpg)

### 基于OSI模型进行扫描的优缺点

1. 二层扫描的优缺点

   优点：扫描速度快、可靠

   缺点：不可路由

2. 三层扫描的优缺点

   优点：可路由，速度较快

   缺点：速度比二层慢，经常被边界防火墙过滤

   使用ip、icmp协议

3. 四层扫描的优缺点

​	优点：可路由且结果可靠，不太可能被防火墙过滤，可以发现所有端口都被过滤的主机

​	缺点：基于状态过滤的防火墙可能过滤扫描，全端口扫描速度慢

## 基于ping命令的探测

### ping

ping命令是常用的判断主机之间网络是否畅通，同样也是能判断我们的目标主机是否存活

```shell
$ ping 172.17.0.3 -c 1
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.018 ms

--- 172.17.0.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.018/0.018/0.018/0.000 ms
```

traceroute命令可以对路由进行跟踪

```shell
$ traceroute wnqinm.cn
traceroute to wnqinm.cn (43.143.70.118), 30 hops max, 60 byte packets
 1  172.17.0.1 (172.17.0.1)  0.025 ms  0.009 ms  0.008 ms
 2  11.73.8.225 (11.73.8.225)  1.134 ms 11.73.8.193 (11.73.8.193)  1.389 ms  1.224 ms
 3  * * *
 4  * * *
 5  * * *
 6  * * *
 7  * * *
```

ping命令也有很多延伸命令

### ARPING

1. ARP协议概述：ARP协议是“Address Resolution Protocol”（地址解析协议）的缩写。计算机通过ARP协议将ip地址转换成MAC地址
2. 原理：向目标计算机询问物理地址，保存在本地
3. 使用arping命令查看局域网中的IP是否有冲突

![arping](https://tva2.sinaimg.cn/large/007Z9xVHgy1h6iw518r6wj31i40e8dtr.jpg)

4. 使用Netdiscover进行被动方式探测局域网中存活的机器

Netdiscover是一个主动/被动的ARP侦查工具。使用Netdiscover工具可以在网络上扫描IP地址，检查在线主机或搜索为它们发送的ARP请求

**主动模式**：顾名思义就是主动探测发现网络内的主机，但是这种方式往往会引起网络管理员的注意

```shell
# -i 指定网卡 -r 指定网段
$ netdiscover -i eth0 -r 192.168.1.0/24
Currently scanning: Finished!   |   Screen View: Unique Hosts

 12 Captured ARP Req/Rep packets, from 3 hosts.   Total size: 720
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname
 -----------------------------------------------------------------------------
 192.168.1.1   00:50:56:c0:00:08     10     600  VMware, Inc.
 192.168.1.2   00:50:56:e2:d2:17      1      60  VMware, Inc.
 192.168.1.254 00:50:56:ea:83:5f      1      60  VMware, Inc.
```

被动模式：更加隐秘，但是速度会比较慢，网卡被设置为混杂模式来侦听网络内的arp数据表进行被动式探测，这种方式就需要网络内设备发送arp包才能被探测到

```shell
$ netdiscover -p
...
```

### hping3

hping3是一个命令行下使用的TCP/IP数据包组装/分析工具，通常web服务会用来做压力测试使用，也可以进行DOS攻击的实验。同样hping只能每次扫描一个目标

```shell
# -c 1000	发送的数据包的数量
# -d 120	发送到目标机器的每个数据包的大小，单位是字节
# -S		只发送SYN数据包
# -w 64		TCP窗口的大小
# -p 80		指定端口
# --flood	尽可能快地发送数据包，不需要考虑显示入站回复。洪水攻击模式
# --rand-source		使用随机的ip地址，这里伪造的ip地址只是在局域中伪造。通过路由器后，还会还原成真实的ip地址
hping3 -c 1000 -d 120 -S -w 64 -p 80 --flood --rand-source xxx.xxx
```

### fping查看局域网中运行了那些机器

fping就是ping命令的加强版，它可以对一个ip段进行ping扫描，而ping命令本身是不可以对网段进行扫描的

```shell
# -g 表示对地址段进行扫描，如果不加可以对某个ip进行扫描
# -c 表示ping的次数
$ fping -g 192.168.1.0/24 -c 1 > fping.txt
```

