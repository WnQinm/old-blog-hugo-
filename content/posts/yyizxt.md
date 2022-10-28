---
title: "运筹学基础"
date: 2022-10-26T15:36:02+08:00
draft: false
tag: ["math"]
categories: 
- 课程笔记
---

运筹学基础

<!--more-->

# 导论

运筹学：将问题建模，得到目标函数和约束条件

高等数学：拉格朗日乘数法，针对多元函数，约束条件得到最优解

二者区别在于运筹学遇到的约束条件往往是不等号，是弱约束条件，后者是强约束条件

运筹学对于非线性的弱约束条件问题，是KKT定理，是拉格朗日乘数法的推广

由拉格朗日/KKT定理求一般的优化问题，回归到特殊的线性规划问题

KKT定理过于一般性，在求解和讨论解的过程会很复杂，不如利用一些更特殊的方法

# 数学基础

## 求最大值

对于一个一元函数 $f(x)$ , 求其在 $[a,b]$ 内的最大值和最小值。

可以通过：$f\'(x)=0$ 求得一系列极值 $\lambda_i$ ，那么给定区间内的最大值一定在 $[f(\lambda_i), f(a), f(b)]$ 之中

## 多元函数最值

引例（瞎编的不保证有解）：<br>
{{< raw >}}
$$
max/min\quad w=f(x,y,z)=3x^2+2y^2-4z^2\\
s.t.
\begin{cases}
g_1:3x+4y-z&=0\\
g_2:6x^2+y-z^2&=0
\end{cases}
$$
{{< /raw >}}
1. 几个约束条件，就引入几个拉格朗日乘子 $\lambda_i$ 
2. 构造一个新函数(拉格朗日函数)：$F(x,y,z,\lambda_i)=f(x,y,z)+\lambda_i g_i$ <br>
   在本题中：<br>
    {{< raw >}}
    $$
    \begin{align*}
    F(x,y,z,\lambda_1,\lambda_2)&=f(x,y,z)+\lambda_1 g_1+\lambda_2 g_2\\
    &=f(x,y,z)+\lambda_1(3x+4y-z)+\lambda_2(6x^2+y-z^2)
    \end{align*}
    $$
    {{< /raw >}}
3. 对每个自变量求偏导，令其为0，得到方程组
   {{< raw >}}
   $$
   \begin{cases}
    \frac{\partial F}{\partial x}=0
   \end{cases}
   $$
   {{< /raw >}}