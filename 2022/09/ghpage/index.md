# Github page域名解析设置


github上修改博客地址为个人购买的域名
<!--more-->

打开github相应的仓库，我博客仓库结构为master储存页面代码，gh-page分支储存blog的public代码

在本地的master分支新建一个CNAME文件，写入注册好的域名（不带前缀），然后重新生成public文件，并将两个分支push到仓库

在DNS提供商网站上设置DNS解析记录：我这里是两个A（@->ghpage的ip，www->ghpage的ip）和一个CNAME（www->ghpage域名）

去仓库的settings->pages中的custom domain发现有刚刚写入CNAME的域名，点一下save等着就好，由于现在已经在dns提供商网站上设置好了域名解析，所以已经可以访问了！

