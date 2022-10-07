---
title: "Nginx(docker)基础配置"
date: 2022-10-08T00:43:07+08:00
draft: false
tags: ["net"]
---

本篇在于利用nginx开启https访问以及设置主页

# 初始化

```shell
# 拷贝容器内配置文件到本地
$ docker run -d -p 80:80 --name nginx \
 -v /home/nginx/html:/usr/share/nginx/html \
 -v /home/nginx/logs:/var/log/nginx \
 nginx:latest

$ docker container cp nginx:/etc/nginx /home/nginx/
$ cd /home/nginx
$ mv nginx conf

# 删除之前的容器
$ docker stop nginx
$ docker rm nginx

# 根据本地配置文件运行新容器
$ docker run -d -p 80:80 -p 443:443 --name nginx \
 -v /home/nginx/conf:/etc/nginx \
 -v /home/nginx/logs:/var/log/nginx \
 -v /home/nginx/html:/usr/share/nginx/html \
 nginx:latest
```

如果不需要配置`ssl`证书开启`https`访问,此时就可以把这个`nginx`当做普通静态服务器使用了

其中`/home/nginx`中的几个文件夹：

+ `conf`：`nginx`的配置文件，具体配置可以看官网
+ `html`：放静态资源，如`html、css、js`等
+ `logs`：日志文件夹，访问的日志可以在其中查看

改了配置文件，需要通过`docker restart nginx`重启`nginx`

# 配置SSL证书开启HTTPS访问

## 申请SSL证书

可以在腾讯云阿里云之类的地方申请的SSL证书

## 证书解压后上传到证书文件夹

在`conf/conf.d`创建`cert`证书文件夹

```shell
cd /home/nginx/conf/conf.d
mkdir cert
```

把下载的证书上传的证书上传到这个文件夹

## 修改nginx配置开启HTTPS访问

`vim /home/nginx/conf/conf.d/default.conf`

写入以下内容：

```shell
server {
    #SSL 访问端口号为 443
    listen 443 ssl;
    #填写绑定证书的域名
    server_name 0101.ml;
    #证书文件名称
    ssl_certificate /etc/nginx/conf.d/cert/0101.ml_bundle.pem;
    #私钥文件名称
    ssl_certificate_key /etc/nginx/conf.d/cert/0101.ml.key;
    ssl_session_timeout 5m;
    #请按照以下协议配置
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;
    location / {
    #网站主页路径。此路径仅供参考，具体请您按照实际目录操作。
        root /usr/share/nginx/html;
        index  index.html index.htm;
    }
}

server {
        listen 80;
        #填写绑定证书的域名
        server_name falser.top;
        #把http的域名请求转成https
        return 301 https://$host$request_uri;
}
```

需要改的地方：

`server_name`：改为自己的域名（两处）

`ssl_certificate`：`.pem`后缀的证书文件

`ssl_certificate_key`：`.key`后缀的证书私钥文件



```html
<!--50x.html-->

<!DOCTYPE html>
<html>
<head>
<title>Error</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>An error occurred.</h1>
<p>Sorry, the page you are looking for is currently unavailable.<br/>
Please try again later.</p>
<p>If you are the system administrator of this resource then you should check
the <a href="http://nginx.org/r/error_log">error log</a> for details.</p>
<p><em>Faithfully yours, nginx.</em></p>
</body>
</html>
```
