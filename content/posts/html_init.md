---
title: "html初识"
date: 2022-09-24T13:15:46+08:00
draft: false
tag: ["net"]
---

# 网页基本信息
+ DOCTYPE
  + 告诉浏览器我们要使用什么规范
+ html
  + 所有内容必须包含在html这个总标签里
+ head
  + 代表网页头部
+ body
  + 代表网页主体
+ title
  + 表示网页标题，显示在标签选项卡处
+ meta
  + 描述性标签
  + 一般用来做SEO
```	html
<meta charset="utf-8"> <!-- 网页编码格式 -->
<meta name="" content=""> <!-- 键值对 -->
```
  键：keywords网页关键词，description网页作用详细描述

# 网页基本标签
**标题标签**

```html
<h1>一级标签</h1>
<h2>二级标签</h2>
```
可一直到六级

**段落标签**
  文字之间不管有多少空格，都只算一个空格
`<p>content</p>`

**换行标签**
`<br/>`
该标签是自闭合标签，也可不加/

**水平线标签**
`<hr/>`
同样是自闭合标签

**粗体斜体**
```html
<!--粗体--> <strong>i love you</strong>
<!--斜体--> <em> i love you </em>
```

**特殊符号**
(常用)
```html
<!--注释--> 快捷键ctrl+/
<!--空格--> &nbsp;
<!--大于--> &gt;
<!-- 小于-->&lt;
<!--版权符号-->&copy;
```

# 图像标签
**常见图像格式**
jpg，png/gif动态图/bmp位图
嵌入图片
```html
<img src="path" alt="text" title="text" width="x" height="y"/>
<!-- scr图像地址(路径)，alt图像的替代文字（如果加载失败），title鼠标悬停提示文字，图像宽度高度-->
```

**地址**
绝对地址：从盘符开始的是绝对地址
相对地址：用`../`代表上一级目录，即从源文件同级文件夹写起地址

**超链接**

+ 页面间链接：从一个页面链接到另一个页面
+ 锚链接
+ 功能性链接

# 链接标签(a标签)

**页面间链接**

```html
<a href="path" target="目标窗口位置">链接文本或图像</a>
<!-- href链接路径，target链接在哪个窗口打开-->
```
链接路径也可直接写文件名，跳转到同一文件夹下目标页面
目标窗口位置常用值：`_self`,`_blank`
链接文本或图像就是链接提示文字或图片，点击进行链接
```html
<!--嵌套图像标签，即点击图片进行跳转-->
<a href="路径">
	<img src="path" alt="text" title="text" width="x" height="y"/>
</a>
```
`target`:表示窗口在哪里打开
如果不设置target，则在当前页面刷新
	_blank：在新标签页打开
	_self:在自己的网页中打开

**锚链接**
1. 需要一个锚标记
2. 跳转到标记

```html
<!--设置锚标记-->
<a name="锚">链接文本或图像</a>  <!--设置标记-->
<a herf="#锚">链接文本或图像</a> <!--跳转到标记-->
<!--锚链接也可进行页面间跳转，先假设该源文件同一文件夹下有源文件‘project.html’-->
<a href="project.html#锚">链接文本或图像</a> <!--跳转到标记-->
```
![屏幕截图 2021-10-12 204625](https://tva4.sinaimg.cn/large/007Z9xVHly1h5dgakyxg9j30pc03o75i.jpg)
在网址后进行锚链接可直接跳转到网页对应位置

**功能性链接**

+ 邮件链接：mailto
+ QQ链接：打开qq推广https://shang.qq.com --推广工具设置即可得到一个a标签


# 行内元素和块元素
+ 块元素
  + 无论内容多少，该元素独占一行
  + p，h1-h6
+ 行内元素
  + 内容撑开宽度，左右都是行内元素的可以排在一行
  + a，strong，em

# 列表
+ 无序列表
+ 有序列表(order  list)
+ 自定义列表

```html
<!--有序列表-->
<ol>
	<li>java</li>
	<li>python</li>
	<li>c/c++</li>
</ol>

<hr/>

<!--无序列表-->
<ul>
	<li>java</li>
	<li>python</li>
	<li>c/c++</li>
</ul>

<hr/>

<!--自定义列表-->
<dl>
	<dt>语言</dt>
	<dd>java</dd>
	<dd>python</dd>
	<dd>c/c++</dd>
	
    <dt>省</dt>
	<dd>Shanxi</dd>
	<dd>Shanghai</dd>
	<dd>Beijing</dd>
</dl>
```
`dl`:标签   `dt`:列表名称   `dd`:列表内容


# 表格
+ 基本结构
  + 单元格
  + 行tr  table rows
  + 列td
  + 跨行
  + 跨列

```html
<!-- 三行三列，属性border边框宽度1px -->
<table border="1px">
	<tr>
		<!--colspan 跨列-->
		<td colspan="3">1-1</td>
		<!--跨三列-->
	</tr>
	<tr>
		<td rowspan="2">2-1</td>
		<td>2-2</td>
		<td>2-3</td>
	</tr>
	<tr>
		<td>3-1</td>
		<td>3-2</td>
	</tr>
</table>
```
![屏幕截图 2021-10-12 234957](https://tva2.sinaimg.cn/large/007Z9xVHly1h5dgcb0i9mj308x04pq2t.jpg)


# 媒体元素
```html
<视频>
<video src="path" controls autoplay></video>
<!--scr资源路径，controls 视频控制条，autoplay自动播放-->

<音频>
<audio src="path" controls autoplay></audio>
```


# 页面结构分析
+ **header**：标题头部区域内容（用于页面或页面中的一块区域）
+ **footer** ：标记脚部区域的内容（用于整个页面或页面的一块区域）
+ **section**：Web页面中的一块独立区域
+ **article**：独立的文章内容
+ **aside**：相关内容或应用（常用于侧边栏）
+ **nav**：导航类辅助内容


# iframe内联框架
```html
<!--src:地址 name:框架表示名-->
<iframe src="path" name="mainFrame" frameborder="0" width="apx" height="bpx" ></iframe>
<!--src和frameborder必有-->
<!--有了name之后可以被作为锚链接对象-->
<a href="path" target="mainFrame">链接文本或图像</a>
<!--target表明该链接打开位置是frame处，iframe创造了一个页面内小窗口
src应该是初始显示，后来会被href覆盖-->
```


# 表单
+ **form标签**：必填action
```html
<form method="" action="">
<!--method常用值get/post，action表示向何处发送表单数据（网站或者请求处理地址）-->
```
**get**
```html
<form action="xxx.html" method="get">
	<p>名字：<input type="text" name="xxx"></p>
	<!--文本输入名字“text”，密码框“password”-->
	<p>密码：<imput type="password" name="xxx"</p>
	
```
get方式提交可以在url处看到我们提交的信息，高效但不安全

**post**(其实和get一样)

```html
<form action="xxx.html" method="post">
	<p>名字：<input type="text" name="xxx"></p>
	<!--文本输入名字“text”，密码框“password”-->
	<p>密码：<imput type="password" name="xxx"</p>
	
```
post比较安全，而且可以传输大文件
审查元素--network--找到第一个包--headers--form data处有表单信息
可以有其他高级方式加密，可在此处也不显示

## 表单元素格式
+ **type**：指定元素的类型。text文本、password密码、checkbox、单选框radio、submit、reset、file、hidden、image、button，默认为text
+ **name**：指定表单的名称
+ **value**：元素的初始值。type为radio时必须指定的值
+ **size**：指定表单元素的初始宽度。当type为text或passwprd时，表单元素的大小以字符为单位。对于其他类型，宽度以像素为单位
+ **maxlength**:type为text或password时，输入的最大字符数
+ **checked**：type为radio或checkbox时，指定按钮是否被选中

```html
<!--单选框-->
<p>
	<input type="radio" value="boy" name="sex"/ checked>男
	<input type="radio" value="girl" name="sex"/>女
</p>
```
name相同，将二者放在同一组别下，实现二选一的效果
后面的汉字时显示的，值是存储起来的

```html
<!--多选框-->
<P>
	<imput type="checkbox" value="sleep" name="hobby">睡觉
	<imput type="checkbox" value="code" name="hobby" checked>敲代码
	<imput type="checkbox" value="chat" name="hobby">聊天
</p>
<!--系统会把name看作数组-->
```
checked默认选中

**按钮**
```html
<P>
	<input type="button" name="xxx" value="xxxx"><!--普通按钮-->
	<input type="image" src="path"><!--图片按钮-->
	<input type="submit"><!--提交按钮-->
	<input type="reset"><!--重置按钮-->
</p>
```

**下拉框**
```html
<p>国家：
	<select name="列表名称">
		<option value="选项的值">值</option>
		<option value="china" selected>中国</option>
		<option value="usa">美国</option>
		<option value="india">印度</option>
	</select>
</p>
```
selected设置默认值

**文本域**
```html
<p>反馈：
	<textarea name="textarea" cols="50" rows="10">文本内容</textarea>
</p>
```
cols列rows行

**文件域**
```html
<p>
	<input type="file" name="files">
</p>
```

**邮件验证**
```html
<p> 邮箱：
	<input type="email" name="email">
</p>
<p> url：
	<input type="url" name="url">
</p>
<p> 数字：
	<input type="number" name="number" max="100" min="0" step="10">
</p>
<!--最大值最小值步长-->

<p>滑块：
	<input type="range" name="voice" min="0" max="100" step="2">
</p>

<p>搜索：
	<input type="search" name="search">
</p>
```

