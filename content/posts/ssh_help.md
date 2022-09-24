---
title: "ssh基础配置"
date: 2022-09-24T13:24:23+08:00
draft: false
tags: ["net"]
---

`systemctl status sshd`查看ssh服务是否启动

`systemctl start sshd`启动ssh服务

通过`ssh 用户名@ip地址`登录服务器

首次登录时需要确认密钥，必须输入完整的yes，然后输入密码即可登录

上面操作十分繁琐，通过公私钥登录服务器

ssh配置文件的位置：

+ Windows下，位于 `%USERPROFILE%\.ssh\config`
+ linux/mac下，位于`~/.ssh/config`

Windows使用一下命令编辑，以防生成后缀名：
```shell
mkdir %USERPROFILE%\.ssh
echo. >> %USERPROFILE%\.ssh\config
notepad %USERPROFILE%\.ssh\config
```

然后将以下内容输入config文件：
```shell
Host 服务器自定义名字
	HostName 服务器公网ip
	Port ssh协议端口
	User 用户名
```

现在便能不用输入用户名，只通过`ssh 自定义名字`来登录服务器

下面设置密钥登录，实现不用输入密码，而且比输入密码登录更安全

`ssh-keygen -t ecdsa`生成密钥对，然后一路回车即可（这里一路回车便设置密钥对密码为空，当然也可以根据提示设置一个密码）

私钥文件为`id_ecdsa`,公钥文件为`id_ecdsa.pub`

通过命令`ssh-copy-id -i ~/.ssh/id_ecdsa.pub 服务器自定义名字`

如果上面命令报错`command not found`，首先输出公钥内容：`type .ssh\id_ecdsa.pub`

然后登录到服务端，`vim ~/.ssh/authorized keys`，将公钥添加到后面，这一个文件可以有多个公钥，注意此文件权限为：`-rw-------`

再编辑前面的config文件：
```shell
Host 服务器自定义名字
	HostName 服务器公网ip
	Port ssh协议端口
	User 用户名
	IdentitiesOnly yes
	IdentityFile 私钥位置
```

Windows写C:\User\\[用户名]\\.ssh\id_ecdsa

