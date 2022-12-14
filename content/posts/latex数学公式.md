---
title: "Latex数学公式"
date: 2022-08-22T20:16:49+08:00
draft: false
tags: ["latex", "math", "blog建设相关"]
categories: 
- Math
---

由于本人技术原因，latex在html界面的渲染做的很差，有的可以正常显示有的则不行，本篇主要是帮助回忆latex数学公式相关语法，故而就此作罢

<!--more-->

双反斜杠换行

# 希腊字母
{{< raw >}}
$$
\delta,\lambda\\
\Delta,\Lambda\\
\Alpha,\Beta\\
\phi,\varphi\\
\epsilon,\varepsilon\\
\partial
$$
{{< /raw >}}

![屏幕截图 2021-10-14 165750](https://tvax3.sinaimg.cn/large/007Z9xVHly1h5dgckyz86j317l0w00we.jpg)

# 上下标

{{< raw >}}

$$
a^2,a_1\\
x^{y+z},p_{ij}\\
x^(y+z),p_ij
$$
上下标内容多于一个字符就需要用大括号包裹

$$
x_i,x_{\rm i},x_{\text i}\\
\text{A B},\rm{A B}\\
\text A B,\rm A B\\
{\rm A} B\\
自然对数{\text e},虚数单位{\text i}
$$
{{< /raw >}}

text支持空格，rm不支持但其后所有都会变成罗马体，line4是解决方法
rm : roman
英文字母只有在表示变量（或单一字符的函数名称，如f(x))时才可使用斜体，其余情况都应使用罗马体

# 分式与根式

{{< raw >}}

分式
$$
\frac {1}{2},\frac 1 2\\
\frac 1 {x+y}\\
\frac {\frac 1 x + 1}{y+1}\\
分子x分之1改大一些：\frac {\dfrac 1 x + 1}{y+1}
$$

根式
$$
\sqrt 2,\sqrt {x+y}\\
\sqrt[3]x
$$

{{< /raw >}}

# 普通运算符

{{< raw >}}

$$
+-\\
\times,\cdot,\div\\
\pm,\mp\\
>,<,\ge,\le,\gg,\ll,\ne,\approx,\equiv\\
\cap,\cup,\in,\notin,\subseteq,\subseteqq,\varnothing\\
\forall,\exists,\nexists\\
\because,\therefore\\
\mathbb R,\R,\N,\Z_+\\
\mathcal F,\mathscr F
$$

$$
\cdots,\vdots,\ddots
$$

$$
\infty,\partial,\nabla,\propto,\degree
$$

$$
\sin x,\sec x,\cosh x\\
\log_2 x,\ln x,\lg x\\
\lim_{x \to 0}\\
\lim\limits_{x \to 0} \frac{x}{\sin x}
\max x
$$

lim限制条件如果不在下面而在右边，可加\limits强行改到下方
注意下划线
运算符英文字母要用直体

{{< /raw >}}

# 大型运算符

{{< raw >}}
$$
\sum,\prod\\
\sum_i,\sum_{i=0}^N\\
\frac{\sum\limits_{i=1}^n x_i}{\prod\limits_{i=1}^n x_i}
$$
注意下划线

$$
\int,\iint,\iiint,\oint,\oiint\\
\int_{-\infty}^0 f(x)\,\text d x
$$

标准微积分d要直立体，且dx要和被积函数拉开一个小距离（\,）


$$
a\, a\\
a\ a\\
a\quad a\\
a\qquad a
$$

{{< /raw >}}

# 标注符号

{{< raw >}}

$$
\vec x,\overrightarrow AB\\
\bar x,\overline{AB}
$$

{{< /raw >}}

![屏幕截图 2021-10-14 175108](https://tva1.sinaimg.cn/large/007Z9xVHly1h5dgdftytfj31cu0fpjt5.jpg)

# 箭头

{{< raw >}}

$$
\leftarrow,\rightarrow,\leftrightarrow,\longleftarrow
$$

{{< /raw >}}

![屏幕截图 2021-10-14 175451](https://tvax1.sinaimg.cn/large/007Z9xVHly1h5dgdo0a7mj30qu0spju1.jpg)

# 括号与定界符

{{< raw >}}

$$
([]),\{ \}\\
\lceil,\rceil,\lfloor,\rfloor,||\\
\left(0,\frac 1 a\right]\\
\left.\frac {\partial f}{\partial x}\right|_{x=0}
$$
括号前加\left\right实现括号大小自适应
像是最后一行左边没括号，可以加一个句点弄一个虚拟括号
{{< /raw >}}

# 多行公式

{{< raw >}}
$$
\begin{align}

a&=b+c+d\\
&=e+f

\end{align}
$$
两处&将自动对齐
{{</ raw >}}

# 大括号

{{< raw >}}

$$
f(x)=

\begin{cases}

\sin x,&-\pi\le x \le \pi\\
0,&\text{其他}
\end{cases}
$$
{{< /raw >}}

# 矩阵

{{< raw >}}
$$
\begin{matrix}

a & b & \cdots & c\\
\vdots& \vdots & \ddots & \vdots\\
e & f & \cdots & g

\end{matrix}
$$

$$
\begin{bmatrix}

a & b & \cdots & c\\
\vdots& \vdots & \ddots & \vdots\\
e & f & \cdots & g

\end{bmatrix}

\begin{pmatrix}

a & b & \cdots & c\\
\vdots& \vdots & \ddots & \vdots\\
e & f & \cdots & g

\end{pmatrix}

\begin{vmatrix}

a & b & \cdots & c\\
\vdots& \vdots & \ddots & \vdots\\
e & f & \cdots & g

\end{vmatrix}
$$

表示矩阵的字符会用加粗罗马体表示,转秩符号用罗马体
$$
\bf A,\bf B^{\rm T}
$$
{{< /raw >}}

# 练习

{{< raw >}}
$$
f(x) = \frac 1 {\sqrt{2\pi} \sigma} {\rm e}^{-\frac {(x-\mu)^2}{2\sigma^2}}\\\\
f(x) = \frac 1 {\sqrt{2\pi} \sigma} \exp \left[{-\frac {(x-\mu)^2}{2\sigma^2}}\right]
$$

$$
\lim\limits_{N\to \infty} P \left\{ \left| \frac {I\left(\alpha_i \right)}{N} - H(s) \right| < \varepsilon \right\} = 1
$$

$$
\begin{align}
&\lim\limits_{x \to 1}(2 - x)^{\sec \frac{\pi x}{2}}\\\\
=&exp \left[\lim\limits_{x \to 1}\frac{\ln (2-x)}{\cos \frac{\pi x}{2}} \right]\\\\
=&exp \left[\lim\limits_{x \to 1}\frac{-\frac{1}{(2-x)}}{-\sin \frac{\pi x}{2}} \cdot \frac 2 \pi \right]\\\\
=&{\rm e}^\frac 2 \pi 
\end{align}
$$

{{< /raw >}}
