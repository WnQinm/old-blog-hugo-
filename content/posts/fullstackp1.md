---
title: "part 1: React 入门"
date: 2022-10-23T23:52:31+08:00
draft: false
tags: ["net"]
categories: 
- 深入浅出现代Web编程 全栈公开课 2022
---

part 1 notes
<!--more-->

# React 简介

&emsp;&emsp;开始需要node环境了，我这里是使用docker进行接下来的学习，这里顺带记录一下大致过程

&emsp;&emsp;pull node相关镜像，我这里 

```shell
docker pull node:18-buster
```

<br>

&emsp;&emsp;然后启动一个容器：
```shell
docker run --name fullstack -p 3000:3000 -it fullstack /bin/bash
```

&emsp;&emsp;这里我还在内网暴露了3000端口（防火墙没开，被挖过一次矿之后开防火墙就十分谨慎了【悲】），但是后来发现利用vscode可以不通过公网访问在容器中临时部署的页面（vscode会弹窗提示，然后open即可）

<br>

&emsp;&emsp;回归主线，根据教程利用`create-react-app`工具制作一个简单的React应用：
```shell
cd /home/node # 下面这行代码会在当前目录生成文件夹，防止混乱cd到主目录
npx create-react-app part1
```

&emsp;&emsp;然后会开始安装不知道什么东西，不过很快报错`permission denied`，奇怪不知道为什么root身份下还会遇到权限问题，发现现在所在的目录`/home/node`拥有者是node，所以`su node`切一下身份，成功运行。

```shell
cd part1
npm start
```

<br>

&emsp;&emsp;接下来让修改文件，突然想起来vscode不能直接操作容器内的文件，但是众所周知vscode什么也能做，搜了一下，安装docker插件即可。

&emsp;&emsp;我这里是使用remote-ssh插件连接远程服务器操作的，安装插件的时候超级慢，又搜了一下，可以通过vscode网站的extensions页面下载vsix后缀的安装包，ftp传到服务器，vscode插件处右上角三个点的`install from vsix`安装即可

<br>

&emsp;&emsp;安装好后问题又来了，插件不能识别到容器和镜像（报错`connect EACCES /var/run/docker.sock`），搜索资料后是因为docker没权限（在此之前我的Ubuntu系统使用docker命令必须加sudo），[需要将当前用户添加至docker用户组](https://docs.docker.com/engine/install/linux-postinstall/)：

```shell
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

&emsp;&emsp;这时就可以发现能正常直接使用docker命令了，不需要sudo了，但是还是需要在控制台重启服务器，不然vscode还是报错（**这里将vscode断开连接重连是不行的，需要在控制台将服务器重启**）

<br>

**简化代码**：

对于`/src/index.js`：

```js
import React from 'react'
import ReactDOM from 'react-dom/client'

import App from './App'

ReactDOM.createRoot(document.getElementById('root')).render(<App />)
```

对于`App.js`：

```js
const App = () => (
  <div>
    <p>Hello world</p>
  </div>
)

export default App
```

删除 App.css, App.test.js, index.css, logo.svg, setupTests.js 和 reportWebVitals.js 文件

<br>

## Component

App.js现在定义了一个名为App的[React组件](https://zh-hans.reactjs.org/docs/components-and-props.html):

```js
ReactDOM.createRoot(document.getElementById('root')).render(<App />)
```

将该组件内容渲染到div-元素中，该元素在`public/index.html`中定义

<br>

再来看一下定义组件的代码：

```js
const App = () => (
  <div>
    <p>Hello world</p>
  </div>
)
```

它被渲染成一个div-标签，包裹着一个p-标签

该组件被定义为一个JavaScript函数：

```js
() => (
  <div>
    <p>Hello world</p>
  </div>
)
```

然后这个函数被分配给一个常量变量App

<br>

有几种方法可以在JavaScript中定义函数，这里使用的是[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，在较新的JavaScript版本中被描述为[ECMAScript 6](http://es6-features.org/#Constants)，也称为ES6

这里用了简写：
```js
(param1, param2, …, paramN) => expression
//相当于：(param1, param2, …, paramN) =>{ return expression; }
```

<br>

定义该组件的函数可以包含任意的js代码

```js
const App = () => {
  console.log('Hello from component')
  return (
    <div>
      <p>Hello world</p>
    </div>
  )
}
```

也可以在一个组件内渲染动态内容：

```js
const App = () => {
  const now = new Date()
  const a = 10
  const b = 20

  return (
    <div>
      <p>Hello world, it is {now.toString()}</p>
      <p>
        {a} plus {b} is {a + b}
      </p>
    </div>
  )
}
```

大括号内的js代码会被计算，然后嵌入到组件产生的HTML中的定义位置