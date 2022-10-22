---
title: "part 0: Web 应用的基础设施"
date: 2022-10-21T00:21:30+08:00
draft: false
tags: ["net"]
categories: 
- 深入浅出现代Web编程 全栈公开课 2022
---

课程相关 & part 0 notes
<!--more-->

# 必要的说明

此笔记仅记录个人在学习过程中认为重要的内容，以及一些总结或心得，不代表课程实际内容，若要查看课程实际的全部内容，参见[赫尔辛基大学推出的全栈公开课：深入浅出现代Web编程 全栈公开课 2022](https://fullstackopen.com/zh/)

&emsp;&emsp;本系列笔记是根据[赫尔辛基大学推出的全栈公开课：深入浅出现代Web编程 全栈公开课 2022](https://fullstackopen.com/zh/) 所记，包括本笔记在内的所有收录于`深入浅出现代Web编程 全栈公开课 2022`的笔记的著作权及版权全归属于此课程官网所提及的人员或组织，具体内容如下，也可进入课程官网查看：

&emsp;&emsp;课程材料的0-8章节和第13章节是<a href="https://github.com/mluukkai">Matti Luukkainen</a> 所写的。第9章节的内容是由来自<a href="https://www.terveystalo.com/fi/Yritystietoa/Terveystalo-tyontantajana/Digital-Health/">Terveystalo</a>的开发者所著。第10部分是由<a href="https://github.com/Kaltsoon">Kalle Ilves</a>编写的。第11部分的内容由来自<a href="https://www.smartly.io/">Smartly</a> 的开发人员编写，第12部分由<a href="https://github.com/jakousa">Jami Kousa</a> 编写。很多<a href="https://github.com/fullstack-hy2020/misc/blob/master/contributors.md">开发者</a>为了改进和修订课程材料做出了贡献，无论是课程内容还是拼写问题^_^。你也可以为这门课程的材料做出自己的贡献。这门课程的网站是由<a target="_blank" href="https://www.houston-inc.com/">Houston inc</a>设计和创建的，它们同样审阅了这门课程的内容。英文版是由Ava Heinonen, Ilkka Kuisma, Harri Mehtälä and Jesper Pettersson 翻译的。中文版是由<a target="_blank" href="https://zhangwei.online/blog">ZhangWei(微信wechat:richardzhangv)</a>翻译的。西班牙语是由<a target="_blank" href="https://github.com/sebastiantorres86">Sebastian Torres</a> 和 <a target="_blank" href="https://github.com/cynthiamv">Cynthia Vico Vacca</a>翻译的.

&emsp;&emsp;这门课程内容的版权在<a href="https://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons BY-NC-SA 3.0 license</a>框架下执行。也就是说你可以自由的使用和分发课程材料，但要保留原作者信息，不能删除他们。如果你想要修改并分发修改后的版本，同样需要遵循这套协议。未经许可，禁止将该材料用于商业目的。

--------------

# a 基础知识

## 先决条件 Prerequisites

说人话就是要求会基础编程，了解网络编程，了解数据库基本概念和操作语法，会git基本操作，能坚持下来<br>不需要对 JavaScript 或其他课程的主题有了解

## Taking the course

最好学熟前一章在学下一章，记得做课后所有没有标记星号的练习题

## Before you start

推荐使用Chrome或者火狐浏览器，网络开发工具很好。

记得安装Git，[这里是网站推荐的git教程](https://product.hubspot.com/blog/git-and-github-tutorial-for-beginners)，我是在[菜鸟教程](https://www.runoob.com/)和b站学习的。

安装支持Web开发的文本编辑器，推荐[Visual Studio Code](https://code.visualstudio.com/)。

同时安装[Node.js](https://nodejs.org/en/)。教材使用的16.13.2版本，不要安装比这更早的版本。参见[Node.js安装说明](https://nodejs.org/en/download/package-manager/)

# b Web 应用的基础设施

## 传统的web应用

进入该页面时，浏览器从服务器上获取描述页面结构的HTML文档，以及文本内容。

服务器以某种方式生成了这个文档。该文档可以是一个保存在服务器目录中的静态文本文件。服务器也可以根据应用代码，例如使用数据库中的数据，动态地形成HTML文档。

类似如下示例代码：

```js
const getFrontPageHtml = (noteCount) => {
  return(`
    <!DOCTYPE html>
    <html>
      <head>
      </head>
      <body>
        <div class='container'>
          <h1>Full stack example app</h1>
          <p>number of notes created ${noteCount}</p>
          <a href='/notes'>notes</a>
          <img src='kuva.png' width='200' />
        </div>
      </body>
    </html>
`)
}

app.get('/', (req, res) => {
  const page = getFrontPageHtml(notes.length)
  res.send(page)
})
```

在传统的 web 应用中，浏览器是个“憨憨”。它只从服务器上获取HTML数据，而所有的应用逻辑都在服务器上。

这个例子使用了Node.js的[Express](https://expressjs.com/)。

## Document Object Model or DOM

我们可以将 html 页面看作隐式树结构。

```
html
  head
    link
    script
  body
    div
      h1
      div
        ul
          li
          li
          li
      form
        input
        input
```

浏览器的工作，就是基于将HTML元素描绘成一棵树。

文档对象模型Document Object Model，或[DOM](https://en.wikipedia.org/wiki/Document_Object_Model)，是一个应用编程接口(API)，它能够对与网页相对应的元素树进行程序化修改。

下面的代码为变量ul创建了一个新节点，并为其添加了一些子节点。

```js
var ul = document.createElement('ul')

data.forEach(function(note) {
  var li = document.createElement('li')

  ul.appendChild(li)
  li.appendChild(document.createTextNode(note.content))
})
```

最后，变量ul的树枝被连接到整个页面的HTML树中的适当位置。

```js
document.getElementById('notes').appendChild(ul)
```

## 利用控制台操作文档对象

一个HTML文档的DOM树的最顶端节点被称为`document`对象。我们可以使用DOM-API在网页上执行各种操作。你可以通过在控制台标签中输入`document`来访问`document`对象。

从控制台向页面添加一个新的笔记

```js
// 首先，我们要从页面上获得笔记的列表。列表在页面的第一个ul-元素中。
list = document.getElementsByTagName('ul')[0]

// 然后创建一个新的li-元素，并在其中添加一些文本内容。
newElement = document.createElement('li')
newElement.textContent = 'Page manipulation from console is easy'

// 然后将新的li-元素加入到列表中。
list.appendChild(newElement)
```

## 表单和HTTP POST方法

```html
<!--form表单-->
<form action="/exampleapp/new_note" method="POST">
      <input type="text" name="note"><br>
      <input type="submit" value="Save">
</form>
```

点击按钮提交表单后，提交了一个对new_note的post请求，然后被302重定向到了notes页面，重新加载notes页面使得我们又向浏览器要来了css、js、json文件

服务器上负责POST请求的代码：

```js
app.post('/new_note', (req, res) => {
  notes.push({
    content: req.body.note,
    date: new Date(),
  })

  return res.redirect('/notes')
})
```

数据被作为POST请求的body发送。

服务器可以通过访问请求对象`req`的`req.body`字段来访问这些数据。

服务器创建一个新的Note对象，并将其添加到一个名为`notes`的数组中。

## AJAX

AJAX(Asynchronous JavaScript and XML)是2005年2月在浏览器技术进步的背景下引入的一个术语，用来描述一种新的革命性的方法，它能够使用包含在HTML中的JavaScript来获取网页内容，而不需要重新渲染网页。

## 单页应用

近年来，出现了创建网络应用的单页应用 (SPA)风格。SPA风格的网站并不像我们的样例应用那样从服务器上单独获取所有的页面，而是只由**一个**从服务器上获取的HTML页面组成，其内容由在浏览器中执行的JavaScript来操作。

我们应用的笔记页面与SPA风格的应用有一些相似之处，但还没有完全达到目的。尽管渲染笔记的逻辑是在浏览器上运行的，但该页面仍然使用传统的方式来添加新的笔记。数据通过表单提交被发送到服务器，服务器通过redirect指示浏览器重新加载笔记页面。

我们的例子应用的单页应用版本可以在[https://studies.cs.helsinki.fi/exampleapp/spa](https://studies.cs.helsinki.fi/exampleapp/spa)找到。

乍看之下，这个应用与之前的应用完全一样。

HTML代码几乎相同，但JavaScript文件不同（spa.js），而且在定义form-tag的方式上有一点变化。

```html
<form id="notes_form">
      <input type="text" name="note"><br>
      <input type="submit" value="Save">
</form>
```

这个表单没有action或method属性来定义如何和往哪里发送输入数据。

当按下按钮提交时可以发现浏览器只发送了一个请求

服务器以状态代码201创建进行响应。这一次服务器没有要求重定向，浏览器停留在同一个页面上，并且没有再发送HTTP请求。

应用的SPA版本没有以传统方式发送表单数据，而是使用了它从服务器上获取的JavaScript代码。

```js
var form = document.getElementById('notes_form')
form.onsubmit = function(e) {
  e.preventDefault()

  var note = {
    content: e.target.elements[0].value,
    date: new Date(),
  }

  notes.push(note)
  e.target.elements[0].value = ''
  redrawNotes()
  sendToServer(note)
}
```

命令`document.getElementById('notes_form')`指示代码从页面上获取表单元素，并注册一个事件处理程序来处理表单提交事件。该事件处理程序立即调用方法`e.preventDefault()`来阻止表单提交的默认处理。默认方法会将数据发送到服务器并导致一个新的GET请求，这是我们不希望发生的。

然后事件处理函数创建了一个新的笔记，用`notes.push(note)`命令将其添加到笔记列表中，重新渲染页面上的笔记列表，并将新笔记发送到服务器。

```js
var sendToServer = function(note) {
  var xhttpForPost = new XMLHttpRequest()
  // ...

  xhttpForPost.open('POST', '/new_note_spa', true)
  xhttpForPost.setRequestHeader(
    'Content-type', 'application/json'
  )
  xhttpForPost.send(JSON.stringify(note))
}
```

该代码确定数据将以HTTP POST请求发送，数据类型为JSON。数据类型由Content-type头决定。然后，数据被作为JSON-字符串发送。