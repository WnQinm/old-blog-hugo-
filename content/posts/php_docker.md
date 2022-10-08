---
title: "php(docker)联动nginx(docker)基础配置"
date: 2022-10-08T00:46:09+08:00
draft: false
tags: ["net"]
---

主页需要用到php文件，所以研究了一下nginx配置php

我这里使用docker安装php

> 这里必须要**吐槽一下**，百度ubuntu安装php，99%都是要yum install php，我搞了半天怎么安装yum（apt install yum提示找不到package，更换了源还是一样），安装yum行不通。
>
> 发现似乎可以直接apt install php，就直接莽了apt install php php-fpm，最后不知道为什么php-fpm找不到，运行不起来，回头看安装提示爆了一堆错，只好卸载php。安装的时候装了不知道多少兆，最后删的时候只删了目测一兆两兆！只好whereis php查询出来一个一个删。。。
>
> 肯定删不干净qwq，**气死我了气死我了气死我了**
>
> 新学了一招 **sudo apt-get autoremove php**卸载很干净，能卸载依赖

正文开始：

`docker pull php:7.4-fpm`拉取镜像，至于为啥不拉取最新的我也不知道，我翻了好几篇文章都是安装指定版本，这里选了一个他们之中最新的

`sudo docker run --name php -v /home/nginx/html:/www -d -p 9000:9000 php:7.4-fpm`**这里是重点**

首先记得防火墙开9000端口，其次我将`/home/nginx/html`映射到了php容器中的`/www`文件，其中`/home/nginx`是我nginx在宿主机的文件映射地址，详情见我的nginx文章，这里的html文件夹用来放置网站的html文件，其在nginx容器中对应地址是`/usr/share/nginx/html`。

找到nginx容器的`default.conf`文件，添加以下东东

```shell
location ~ \.php$ {
       root           /www;
       fastcgi_pass   服务器公网ip:9000;
       fastcgi_index  index.php;
       fastcgi_param  SCRIPT_FILENAME  /www/$fastcgi_script_name;
       include        fastcgi_params;
    }
# 第二行根目录存放的是php文件，这里与nginx存放html文件的文件夹映射到了宿主机同一文件夹下
# 第三行填服务器或者你的电脑的公网ip地址，这里注意需要把nginx和php放到同一docker network下，不然可能会发生不好的事情
# 第五行/www/这里也是存php文件的那个目录，具体啥意思我也不没看懂，详情见菜鸟教程相关说明
```

现在，如果你在宿主机的html目录下放置了php文件，就能通过:服务器ip/file.php访问了！

随文附上我的nginx配置文件default.conf:

```shell
# code-server二级域名配置
server {
    listen       80;
    server_name  xxx;

    location / {
        proxy_pass http://ip:xxxx/;
	proxy_set_header Host $host;
	proxy_set_header Upgrade $http_upgrade;
	proxy_set_header Connection upgrade;
	proxy_set_header Accept-Encoding gzip;
    }
}

# http转https
server{
    listen 80;
    listen [::]:80;
    server_name xxx;

    return 301 https://$host$request_uri;
}

# https配置
server {
    listen       443 ssl;
    server_name  xxx;

    #access_log  /var/log/nginx/host.access.log  main;
    
    ssl_certificate /etc/nginx/conf.d/cert/xxx_bundle.pem;
    
    ssl_certificate_key /etc/nginx/conf.d/cert/xxx.key;
    ssl_session_timeout 5m;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_prefer_server_ciphers on;
# 此处便是域名根目录地址，即前文所说的html文件储存地址
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}
# php相关配置
    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
       root           /www;
       fastcgi_pass   ip:9000;
       fastcgi_index  index.php;
       fastcgi_param  SCRIPT_FILENAME  /www/$fastcgi_script_name;
       include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```
