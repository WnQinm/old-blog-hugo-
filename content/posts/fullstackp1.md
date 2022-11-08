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

## 组件-Component

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

<br>

## JSX

&emsp;&emsp;主观来看React组件返回了一个HTML标记，但是事实上React组件的布局大多是用[JSX](https://reactjs.org/docs/introducing-jsx.html)编写的。虽然JSX很像是HTML，但其实我们在处理一种写JS的方式。底层上，由React组件返回的JSX 被编译成JS。

&emsp;&emsp;编译后如下所示：

```js
const App = () => {
  const now = new Date()
  const a = 10
  const b = 20
  return React.createElement(
    'div',
    null,
    React.createElement(
      'p', null, 'Hello world, it is ', now.toString()
    ),
    React.createElement(
      'p', null, a, ' plus', b, ' is ', a+b
    )
  )
}
```

&emsp;&emsp;编译是由[Babel](https://babeljs.io/repl/)处理的。用`create-react-app`创建的项目会被配置为自动编译。

&emsp;&emsp;可以把React写成类似上面的“纯JS”而不使用JSX，但是会很麻烦。

&emsp;&emsp;JSX很像HTML，区别在于JSX可以通过在大括号内编写适当的JS代码来嵌入动态内容。

&emsp;&emsp;JSX是“XML-like”语言，这意味着所有标签必须闭合。譬如换行标签：

```html
<!--HTML-->
<br>

<!--JSX-->
<br />
```

<br>

## 多个组件-Multiple components

&emsp;&emsp;修改文件 App.js 如下：

```js
const Hello = () => {
  return (
    <div>
      <p>Hello world</p>
    </div>
  )
}

const App = () => {
  return (
    <div>
      <h1>Greeting</h1>
      <Hello />
    </div>
  )
}
export default App;
```

<br>

&emsp;&emsp;这里定义了一个新组件 Hello，并在组件 App 中使用它。一个组件也可以被多次使用：

```js
const App = () => {
  return (
    <div>
      <h1>Greeting</h1>
      <Hello />
      <Hello />
      <Hello />
    </div>
  )
}
```

<br>

&emsp;&emsp;通过组合组件，即使是比较复杂的应用也可以保持相当的可维护性。事实上React的一个核心理念就是由许多专门的可重复使用的组件组成应用。

&emsp;&emsp;另一个强制的惯例是在应用的组件树的顶端有一个叫做App的根组件。然而有些情况下，组件的App并不完全是根，而是被包裹在一个适当的实用组件中。

<br>

## 参数-props: passing data to components

&emsp;&emsp;可以使用所谓的[props](https://zh-hans.reactjs.org/docs/components-and-props.html)向组件传递数据。

&emsp;&emsp;对组件Hello做如下修改

```js
const Hello = (props) => {
  return (
    <div>
      <p>Hello {props.name}</p>
    </div>
  )
}
```

&emsp;&emsp;现在定义组件的函数有一个参数props。该参数接收一个对象，该对象有对应于组件用户定义的所有“props”的字段。

&emsp;&emsp;App组件修改如下：

```js
const App = () => {
  return (
    <div>
      <h1>Greeting</h1>
      <Hello name="George" />
      <Hello name="Daisy" />
    </div>
  )
}
```

&emsp;&emsp;可以有任意数量的prop，它们的值可以是“硬编码”的字符串或JS表达式的结果。如果prop的值是用JS实现的，必须用大括号包裹起来。

<br>

&emsp;&emsp;修改代码，让组件Hello使用两个props：

```js
const Hello = (props) => {
  return (
    <div>
      <p>
        Hello {props.name}, you are {props.age} years old
      </p>
    </div>
  )
}

const App = () => {
  const name = 'Peter'
  const age = 10

  return (
    <div>
      <h1>Greetings</h1>
      <Hello name="Maya" age={26 + 10} />
      <Hello name={name} age={age} />
    </div>
  )
}
```

&emsp;&emsp;类似于每定义了一个Hello标签，就会定义一个对应的对象，这个对象会作为实参传入组件内部，标签的属性翻译成了这个对象的属性。

## 一些提示-Some notes

+ React已经能生成相当清晰的错误信息。尽管如此，还是建议每次修改只改动一点，以保证每次改变都是顺利的。

<br>

+ 控制台应时刻打开，以监视错误以及了解错误原因。

<br>

+ 类似于python的print调试大法，在React中也可以使用`console.log()`进行调试。

<br>

+ **React的组件名称必须首字母大写**，否则会报错：

  如果尝试用以下方式定义一个组件：

  ```js
  const footer = () => {
  return (
    <div>
      greeting app created by <a href="https://github.com/WnQinm">WnQinm</a>
    </div>
  )
  }
  ```

  并这样使用它：

  ```js
  const App = () => {
  const name = 'Peter'
  const age = 18
  return (
    <div>
      <h1>Greeting</h1>
      <Hello name="George" age={1+2} />
      <Hello name={name} age={age} />
      <footer />
    </div>
  )
  }
  ```

  React 只会创建一个空的footer元素，即内置的HTML元素，而不是同名的自定义的React元素。如果吧组件名称的第一个字母改为大写字母，那么React就会创建一个定义在Footer组件中的div元素，并在页面渲染。

<br>

+ 注意，React组件的内容通常需要包含一个根元素。例如，如果我们试图定义组件App而不使用最外层的div元素。

  ```js
  const App = () => {
  return (
      <h1>Greeting</h1>
      <Hello name="George" age={1+2} />
      <Footer />
  )
  }
  ```

  页面就会返回错误信息：

  ![fullstack_somenotes](https://tva1.sinaimg.cn/large/007Z9xVHgy1h7mo9iqojqj30xs0h30yd.jpg)

  使用根元素不是唯一的选择。一个组件的array也是一个解决方案：

  ```js
  const App = () => {
  return [
      <h1>Greeting</h1>,
      <Hello name="George" age={1+2} />,
      <Footer />
  ]
  }
  ```

  但是这样会使得代码有些难看。

  由于根元素被强制规定必须有div了，我可在DOM树中有“额外的”div-elements。这可以通过使用[fragments](https://reactjs.org/docs/fragments.html#short-syntax)来避免，即用一个空元素来包装组件要返回的元素：

  ```js
  const App = () => {
  return (
    <>
      <h1>Greeting</h1>
      <Hello name="George" age={1+2} />
      <Footer />
    </>
  )
  }
  ```

# JavaScript

&emsp;&emsp;这个课程使用了较新的JS版本的功能。JavaScript标准的官方名称是[ECMAScript](https://en.wikipedia.org/wiki/ECMAScript)。

&emsp;&emsp;浏览器还不支持JS的所有最新功能。由于这个事实，很多浏览器中运行的代码都是从较新版本的JS转写成较旧的、更兼容的版本。

&emsp;&emsp;如今最流行的方式是通过使用[Babel](https://babeljs.io/)进行转码。在用create-react-app创建的React应用中，转移是自动配置的。

&emsp;&emsp;[Node.js](https://nodejs.org/en/)是一个基于Google's [Chrome V8](https://developers.google.com/v8/) JavaScript引擎的JS运行环境，几乎可以在任何地方工作（从服务器到手机应用）。如果机器上安装的Node.js版本>=16.13.2，那么Node可以理解最新版本的JS代码，代码不需要转码。**（2022.11.5）**

&emsp;&emsp;代码被写入以`.js`结尾的文件中，通过键入`node name_of_file.js`命令来运行。

&emsp;&emsp;也可以将JS代码写入Node.js控制台，该控制台可以通过在命令行输入node打开，也可以写入浏览器的开发者工具控制台（比如Chrome、Firefox）。

## 变量-Variables

&emsp;&emsp;在JS中，有几种方法来定义变量。

```js
const x = 1
let y = 5

console.log(x,y)
y += 10
console.log(x,y)
y = 'sometext'
consolek.log(x,y)
x = 4                 // cause an error
```

&emsp;&emsp;const定义一个常量，let定义了一个普通变量。而且分配给变量的数据类型可以改变。

&emsp;&emsp;还有一种使用var来定义变量的方法，在之前的很长一段时间内，var是定义变量的位移方法。详情参见[JavaScript变量--你应该使用let、var还是const? on Medium](https://medium.com/craft-academy/javascript-variables-should-you-use-let-var-or-const-394f7645c88f)或[关键词：var vs. let on JS Tips](http://www.jstips.co/en/javascript/keyword-var-vs-let/)，YouTube上也有相关视频：[var, let and const - ES6 JavaScript Features](https://youtu.be/sjyJBL5fkp8)。此课程建议全程使用const和let。

## 数组Arrays

```js
const t = [1,-1,3]

t.push(5)

console.log(t.length)
console.log(t[1])

t.forEach(value => {
  console.log(value)
})
```

&emsp;&emsp;注意数组的内容可以修改，原因是数组是一个对象，这个变量总是指向同一个对象。然而，数组的内容随着新项目的加入而改变。

&emsp;&emsp;遍历数组项目的一种方法是使用forEach ，如图所示。forEach接收一个用箭头语法定义的**函数**作为参数。

```js
value => {
  console.log(value)
}
```

&emsp;&emsp;forEach为数组中的每个项调用函数，总是传递单个项作为参数。作为forEach参数的函数也可以接收其他参数。

&emsp;&emsp;在前面的例子中，使用方法push将一个新项添加到数组中。在使用React时，经常使用函数式编程的技术。函数式编程范式的一个特点是使用不可变的数据结构。在React代码中，最好使用concat方法，该方法不会将项目添加到数组中，而是创建一个新的数组，其中同时包含旧数组和新项目的内容。

```js
const t = [1,-1,3]

const t2 = t.concat(5)

console.log(t)
console.log(t2)
```

&emsp;&emsp;下面是一个使用map方法进行数组定义的例子。

···js
const t = [1,2,3]

const m1 = t.map(value => value * 2)
console.log(m1)
```

&emsp;&emsp;Map也可以将数组转化为完全不同的东西。

```js
const m2 = t.map(value => '<li>' + value + '</li>')
console.log(m2)
// ['<li>1</li>','<li>2</li>','<li>3</li>']
```

&emsp;&emsp;在这里，一个充满整数值的数组通过map方法被转化为一个包含HTML字符串的数组。在本课程的第二章节中，我们将看到map在React中使用的相当频繁。

&emsp;&emsp;在解构赋值（类似python）的帮助下，数组中的单个项目很容易被赋值给变量。

```js
const t = [1,2,3,4,5]

const [first,second,...rest] = t

console.log(first,second)
console.log(rest)
```

&emsp;&emsp;变量first和second以数组前两个整数作为它们的值。其余的整数作为一个数组传入给rest变量。

## 对象-Objects

&emsp;&emsp;在JS中，有几种不同的方法来定义对象。一种非常常见的方法是使用对象字面量(字面量是由语法表达式定义的变量;或通过由一定字词组成的语词表达式定义的变量)
