---
title: "html meta标签简介"
date: 2022-10-08T00:39:16+08:00
draft: false
tags: ["net"]
---

# meta标签特性
+ meta标签是单标签，格式如下
	`<meta 属性="值"/>`
+ meta标签永远位于head元素内部
+ meta标签分两大部分：http标题信息(http-equiv) 和页面描述信息（name）

# meta标签的http标题信息
http-equiv相当于http的文件头作用，它可以向浏览器传回一些有用的信息，以帮助正确和精确地显示网页内容
http-equiv属性语法格式：`<meta http-equiv="参数" content="参数变量值"/>`
==主要有以下几种参数：==
1. expires（期限）
	可以用于设定网页的到期时间。一旦网页过期，必须到服务器上重新传输。
	`<meta http=equiv="expires" content="Wed,20 June 2016 10:30:00 GMT">`
	注：必须使用GMT时间格式
2. pragma（cache模式）
    禁止浏览器从本地计算机的缓存中调阅页面内容
    `<meta http-equiv="pragma" content="no-cache"`
    注：这样设定，访问者将无法脱机浏览
3. refresh（刷新）
    定时刷新让网页自动链接到其他网页
    `<meta http-equiv="refresh"content="5;URL=//http://www.baidu.com"/>`
    注：其中的5是指停留5秒钟后自动刷新到URL网址，秒数和URL直接用分号隔开
4. content-type（显示字符集的设定）
    设定页面使用的字符集
    `<meta http-equiv="content-type" content="text/html;charset=utf-8"/>`

  charset选项：

  + utf-8 （utf-8包含全世界所有国家需要用到的字符，是国际编码，通用性强，又称万国码。如：阿里云，淘宝网，等都采用这种编码方式）
  + GBK （GBK是包含中日韩字符的大字符集合。如：京东网采用的是GBK编码）
  + GB2312 （GB2312是GBK的子集，是一种简体中文编码，通行于中国大陆。如：腾讯网、网易等都采用该种编码）

# meta标签的页面描述信息
name是描述网页的，对应于content（网页内容），以便于搜索引擎机器人查找、分类，目前几乎所有的搜索引擎都使用网上机器人自动查找meta值来给网页分类
name属性语法格式：`<meta name="参数" content="具体参数值"/>`
==主要有以下几种参数：==
1. keywords（关键字）
	告诉搜索引擎你网页的关键字是什么
	`<meta name="keywords" content="网上购物,网上商城,手机,笔记本,电脑,MP3,CD,VCD,DV,相机,数码,配件,手表,存储卡,京东"/>`
	关键词之间用英文逗号隔开
2. description（网页描述）
	告诉搜索引擎你的网页的主要内容
	`<meta name="description" content="京东JD.COM-专业的综合网上购物商城,销售家电、数码通讯、电脑、家居百货、服装服饰、母婴、图书、食品等数万个品牌优质商品.便捷、诚信的服务，为您提供愉悦的网上购物体验!">`
3. robots（机器人向导）
	robots用来告诉搜索机器人哪些页面需要索引，哪些页面不需要索引。
	content的参数有all、none、index、noindex、follow、nofollow。默认是all。
	`<meta name="robots" contents="all"/>`
	参数说明：
	+ all：搜索引擎将索引此网页，且页面上的链接可以被查询，等价于index和follow
	+ none：搜索引擎将忽略此网页，且页面上的链接不可以被查询，等价于noindex和nofollow
	+ index：文件将被索引
	+ follow：搜索引擎通过此网页的链接，索引搜索其他网页
	+ noindex：文件将不被检索，但网页中的链接，可以索引搜索其它的网页
	+ nofollow：文件将不被检索，搜索引擎不可以通过此网页的链接，继续索引其它网页
4. author（作者）
	标注网页的作者或制作组
	`<meta name="author" content="作者的名字"/>`
