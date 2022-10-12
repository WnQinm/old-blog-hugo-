---
title: "处理linux病毒'kinsing''kdevtmpfsi'"
date: 2022-10-10T09:43:25+08:00
draft: false
tags: ["virus","net"]
categories:
- Hack
---

docker安装php并对外开放9000端口极易被攻击！
<!--more-->

# 简要描述

连续两天出现阿里云和腾讯云提示恶意脚本运行和挖矿软件，都是在开启php的docker容器之后发生的，不过网上大部分同时关于`docker`和`kdevtmpfsi`的文章都是在说是`docker-redis`的原因，一时没有找到比较符合我这里情况的被入侵原因，今天突然无意间看到了符合我情况的解释

# 解决过程简述

## 杀死进程

```shell
# 找到病毒进程
ps -aux | grep kinsing
ps -aux | grep kdevtmpfsi
# 结束进程
sudo kill -9 pid
```

## 删除文件和定时任务

```shell
# 寻找文件位置
su # 登录root
find / | grep kinsing
find / | grep kdevtmpfsi
rm -rf ... # 删除搜索到的文件

cd /var/spool/cron/crontabs # 查找定时任务
rm -rf ... # 删除可疑任务
```

## 漏洞原因

`php-fpm fastcgi`漏洞，资料如下：

> https://www.leavesongs.com/PENETRATION/fastcgi-and-php-fpm.html
>

大意是：**开放了9000端口，按照一定的代码格式，来请求php代码，进入fastcgi里面，就可以执行自己任意的php代码**

关闭9000端口，使用docker网桥连接nginx和php容器，`docker network inspect bridge`查看php的内网ip，nginx的`default.conf`文件中关于php的设置，直接访问ip:9000即可

# 总结

处理恶意程序的大致方法如下：

1. 杀死挖矿程序进程（注意先kill守护进程）
2. 查看定时任务，有无可疑程序
3. 删除相关文件，重新新建文件，然后设置权限
4. 观察，查看log

    不必要的端口不要暴露出来