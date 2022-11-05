---
title: "信息泄露"
date: 2022-10-20T00:26:26+08:00
draft: false
tags: ["net"]
categories: 
- ctf
---

# 备份文件下载

## 网站源码

### 题目

当开发人员在线上环境中对源代码进行了备份操作，并且将备份文件放在了 web 目录下，就会引起网站源码泄露。<br>日常url

### 解题过程

打开网址后得到了一些提示：

> 常见的网站源码备份文件后缀: `tar, tar.gz, zip, rar`<br>
> 常见的网站源码备份文件名: `web, website, backup, back, www, wwwroot, temp`

写代码遍历一下目录下有木有类似的备份文档:
```python
import requests

url = "http://challenge-e6f8bdc6cd89dde1.sandbox.ctfhub.com:10800/"
suffix_list = ['tar', 'tar.gz', 'zip', 'rar']
name_list = ['web', 'website', 'backup', 'back', 'www', 'wwwroot', 'temp']
for suffix in suffix_list:
    for name in name_list:
        fullname = f'{name}.{suffix}'
        fullurl = url + fullname
        req = requests.get(fullurl)
        if req.status_code != 404:
            print(fullurl)
```

得到文件名后访问下载，得到网站备份和一个名为flag的txt文件，但是打开文件被调戏了一番：`where is flag ??`

既然是网站备份，那就在现运行网站访问一下这个txt文件，最后得到flag

## bak文件

### 题目

当开发人员在线上环境中对源代码进行了备份操作，并且将备份文件放在了 web 目录下，就会引起网站源码泄露。

### 解题过程

打开所给的url，提示：Flag in index.php source code.

进入`url/index.php`提示依旧如上，根据题目进入`url/index.php.bak`

得到flag

<b>注：</b>通过dirsearch可以遍历web目录：<br>&emsp;&emsp;`dirsearch -u [url] -e *`(kali) 或 `python3 dirsearch.py -u [url] -e *`

## vim缓存

### 题目

当开发人员在线上环境中使用 vim 编辑器，在使用过程中会留下 vim 编辑器缓存，当vim异常退出时，缓存会一直留在服务器上，引起网站源码泄露。

### 解题过程

打开网址，提示：flag 在 index.php 源码中

由题目提示，根据多次用vim不小心异常退出生成备份文件的经验依稀记得是生成类似：`[filename].swp` 的文件

使用 `dirsearch -u [url] -e * | grep index.php` 查找，果然有一个 `.index.php.swp`文件，下载下来之后得到了flag（在很多空格之后的最末尾，差点以为没有）

## .DS_Store

### 题目

.DS_Store 是 Mac OS 保存文件夹的自定义属性的隐藏文件。通过.DS_Store可以知道这个目录里面所有文件的清单。

### 解题过程

打开网址，根据题目提示下载这个隐藏文件

得到：$ 9 5 8 b 9 9 e f 0 6 0 6 4 f 9 1 c 0 8 d c e c 5 6 d 8 1 3 c c a . t x tnoteustr    f l a g   h e r e ! 

访问：`[url]/958...cca.txt`得到flag

# Git泄露

## log

### 题目

当前大量开发人员使用git进行版本控制，对站点自动部署。如果配置不当,可能会将.git文件夹直接部署到线上环境。这就引起了git泄露漏洞。请尝试使用BugScanTeam的GitHack完成本题

### 解题过程

根据题目指引去下载了GitHack工具：`git clone https://github.com/BugScanTeam/GitHack.git`，工具也可以在ctfhub的工具页面中搜索到。

然后我使用python3运行工具，发现报了一堆错，在改bug的时候意识到这是用python2写的，，，

运行`python2 GitHack.py [url]`获取了.git目录，`git log`发现上一条记录是删除flag，`git diff HEAD^`查看一下删除内容，获取flag

## Stash

### 题目

当前大量开发人员使用git进行版本控制，对站点自动部署。如果配置不当,可能会将.git文件夹直接部署到线上环境。这就引起了git泄露漏洞。请尝试使用BugScanTeam的GitHack完成本题

### 解题过程

没用过git的这个功能，搜索资料知道可以通过`git stash pop`撤销上一次修改（大概似乎是这个意思）

依然`python2 GitHack.py [url]`获取.git目录，`git stash pop`获得一个txt文档，里面便有flag

