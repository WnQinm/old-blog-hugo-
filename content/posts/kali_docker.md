---
title: "Kali(docker)"
date: 2022-09-24T13:26:40+08:00
draft: false
tag: ["net"]
---

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

