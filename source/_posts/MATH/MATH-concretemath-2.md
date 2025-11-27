---
title: 第二章 和式
category_bar: true
math: true
tags: [具体数学, 和式, 有限微积分]
category: [数理基础, 《具体数学》学习笔记]
date: 2025-11-27 18:49:02
---

# 和式和递归式
对形如$a\_nT\_n=b\_nT\_{n-1}+c\_n$的式子求和。
考虑私用一个求和因子$s\_n$，变形为：
$$
s\_na\_nT\_n=s\_nb\_nT\_{n-1}+s\_nc\_n,
$$
并使得
$$
s\_nb\_n=s\_{n-1}a\_{n-1}.
$$
此时记$S\_n=s\_na\_nT\_n$则得到一个和式-递归式
$$
S\_n=S\_{n-1}+s\_nc\_n.
$$
现在会变得易于求解，解得
$$
S\_n=s\_0a\_0T\_0+\sum\_{k=1}^n s\_kc\_k=s\_1b\_1T\_0+\sum\_{k=1}^n s\_kc\_k,
$$
所以原式的解为
$$
T\_n=\frac{1}{s\_na\_n}\left(s\_1b\_1T\_0+\sum\_{k=1}^ns\_kc\_k \right),
$$
求和因子为
$$
s\_n=\frac{a\_{n-1}a\_{n-2}\cdots a\_1}{b\_nb\_{n-1}\cdots b\_2}.
$$

# 和式的处理
## 扰动法
记$S\_n=\sum\_{0\leqslant k\leqslant n} a\_k$，将第一项和最后一项分离出来，重写$S\_{n+1}$：
$$
\begin{align}
S\_n+a\_{n+1}=\sum\_{0\leqslant k\leqslant n+1} a\_k &= a\_0+\sum\_{1\leqslant k\leqslant n+1} a\_k \newline
&= a\_0+\sum\_{0\leqslant k\leqslant n} a\_{k+1}
\end{align}
$$
如果可以使用$S\_n$表示$\sum\_{0\leqslant k\leqslant n} a\_{k+1}$,那么就可以得到一个$S\_n$的方程并求解。
例如对于几何级数$S\_n=\sum\_{0\leqslant k\leqslant n}ax^k$，得到：
$$
\begin{align}
S\_n+ax^{n+1} = ax^0+\sum\_{0\leqslant k \leqslant n}ax^{k+1} \newline
 = ax+x\sum\_{0\leqslant k \leqslant n} ax^k = ax+S\_n \newline
 \Rightarrow S\_n = \frac{a(1-x^{n+1})}{1-x}, \quad x\neq 1
\end{align}
$$
可以发现其实就是错位相减法。

# 一般性的方法
这里给出几种方法：
1. 查表法
2. 猜测，归纳法证明
3. 扰动法
4. 建立成套方法
5. 积分替换和式
6. 展开和收缩
7. 有限微积分
8. 生成函数

## 成套方法

## 积分替换和式
例如求解前$n$项自然数的平方和$S\_n$，可以认为等于计算一系列$1\times 1, 1\times 4, \cdots , 1\times n^2$的矩形面积和。容易知道近似值是函数$f(x)=x^2$的积分。设误差$E\_n = S\_n - \int\_0^n x^2\mathrm{d} x=S\_n-\frac{n^3}{3}$。
对于求解误差有两种方法。
1.得到关于$E\_n$的一个递归式。
$$
\begin{align}
E\_n & = S\_n-\frac{n^3}{3} = S\_{n-1}+n^2-\frac{n^3}{3}=E\_{n-1}+\frac{(n-1)^3}{3}+n^2-\frac{n^3}{3} \newline
& = E\_{n-1}+n-\frac{1}{3}
\end{align}
$$
2.对楔形误差项的面积求和
$$
\begin{align}
S\_n-\int\_{0}^nx^2\mathrm{d}x &= \sum\_{k=1}^n\left( k^2-\int\_{k-1}^k x^2\mathrm{d}x\right) \newline
&= \sum\_{k=1}^n\left( k^2-\frac{k^3-(k-1)^3}{3}\right) = \sum\_{k=1}^n\left( k-\frac{1}{3}\right).
\end{align}
$$
## 展开和收缩
同样考虑前$n$项自然数的平方和。
$$
\begin{align}
S\_n &= \sum\_{1\leqslant k\leqslant n} k^2 = \sum\_{1\leqslant j\leqslant k\leqslant n} k \newline
&= \sum\_{1\leqslant j\leqslant n} \sum\_{j\leqslant k\leqslant n} k \newline
&= \sum\_{1\leqslant j\leqslant n} \left( \frac{j+n}{2}\right)(n-j+1) \newline
&= \frac{1}{2}\sum\_{1\leqslant j\leqslant n}\left(n(n+1)+j-j^2\right) \newline
&= \frac{1}{2} n^2(n+1)+\frac{1}{4}n(n-1) -\frac{1}{2}S\_{n} \newline
&= \frac{1}{2} n\left(n+\frac{1}{2}\right)(n+1) - \frac{1}{2}S\_n.
\end{align}
$$

# 有限微积分和无限微积分
无限微积分基于微分定义$Df(x) = \lim\_{h\rightarrow 0}\frac{f(x+h)-f(x)}{h}$。类似的，定义**差分**算子：
$$
\Delta x = f(x+1)-f(x).
$$
例如$\Delta (x^3) = (x+1)^3-x^3=3x^2+3x+1$。
## 阶乘幂
下降阶乘幂定义为：
$$
x^{\underline{m}} = x(x-1)(x-2)\cdots (x-m+1), \quad m\in \mathbb{N}.
$$
上升阶乘幂定义为：
$$
x^{\overline{m}} = x(x+1)(x+2)\cdots (x+m-1), \quad m\in \mathbb{N}.
$$
特别地，当$m=0$时，满足$x^{\underline{0}}=x^{\overline{0}}=1$。
与阶乘的关系：
$$
n! = n^{\underline{n}}=1^{\overline{n}}.
$$
下降阶乘幂对于差分运算有良好的性质，
$$
\begin{align}
\Delta(x^{\underline{m}}) &= (x+1)^{\underline{m}}-x^{\underline{m}} \newline
&= (x+1)x(x-1)\cdots(x-m+2)-x\cdots(x-m+2)(x-m+1) \newline
&=mx(x-1)\cdots(x-m+2) \newline
&= mx^{\underline{m-1}}.
\end{align}
$$
扩展定义，对于小于0的指数，
$$
x^{\underline{-m}} = \frac{1}{(x+1)(x+2)\cdots(x+m)}, \quad m>0.
$$
特别地，$x^{\underline{-1}}=\frac{1}{x+1}$。
满足幂法则：$x^{\underline{m+n}}=x^{\underline{m}}(x-m)^{\underline{n}},m,n\in\mathbb{Z}$。

## 不定和式与确定的和式
类似微积分基本定理，差分算子$\Delta$及其逆运算求和算子$\Sigma$，满足：
$$
g(x)=\Delta f(x) \text{当且仅当} \sum g(x)\delta x = f(x)+C.
$$
$\sum g(x)\delta x$称为$g(x)$的不定和式，是差分等于$g(x)$的一个函数类。$C$是满足$p(x+1)=p(x)$的任意一个函数，在$x$的整数值处$C$是常数。
类比定积分定义：
$$
\sum\_a^b g(x)\delta x= \sum\_{a\leqslant k < b} g(k)= \left.f(x)\right|\_a^b = f(b)-f(a)
$$
在这里要求$a\leqslant b$。特别地，当$a=b$时，$\sum\_a^a g(x)\delta x = 0$。
进行扩展：当$a>b$时，有$\sum\_a^b g(x)\delta x = -\sum\_b^a g(x)\delta x$。
于是可以得到一个恒等式：
$$
\forall a, b, c\in \mathbb{Z}, \sum\_a^b g(x)\delta x+\sum\_b^c g(x)\delta x=\sum\_a^cg(x)\delta x.
$$
利用上面这些公式可以快捷地计算一些求和。
例如：
$$
\sum\_{0\leqslant k < n} k^{\underline{m}} =\left. \frac{k^{\underline{m+1}}}{m+1}\right|\_0^n=\frac{n^{\underline{m+1}}}{m+1}, \quad n\in\mathbb{N},m\in \mathbb{Z}\text{且}m\neq -1.
$$
$$
\sum\_{0\leqslant k< n}k^2=\sum\_{0\leqslant k <n}(k^{\underline{2}}+k^{\underline{1}})=\frac{n^{\underline{3}}}{3}+\frac{n^{\underline{2}}}{2}.
$$
通过斯特林数可以在幂和阶乘幂之间转换。

## 和式表

| $f=\sum g$                        | $\Delta f = g$                     |
| --------------------------------- | ---------------------------------- |
| $x^{\underline{0}}=1$             | 0                                  |
| $x^{\underline{1}}$               | 1                                  |
| $x^{\underline{2}}=x(x-1)$        | $2x$                               |
| $x^{\underline{m}}$               | $mx^{\underline{m-1}}$             |
| $\frac{x^{\underline{m+1}}}{m+1}$ | $x^{\underline{m}}$                |
| $H\_x$                            | $x^{\underline{-1}}=\frac{1}{x+1}$ |
| $2^x$                             | $2^x$                              |
| $c^x$                             | $(c-1)c^x$                         |
| $\frac{c^x}{c-1}$                 | $c^x$                              |
| $cu$                              | $c\Delta u$                        |
| $u+v$                             | $\Delta u+\Delta v$                |
| $uv$                              | $u\Delta +Ev\Delta u$              |

## 分部求和
$$
\begin{align*}
\Delta(u(x)v(x)) &= u(x+1)v(x+1)-u(x)v(x) \newline
&= u(x+1)v(x+1)-u(x)v(x+1)+u(x)v(x+1)-u(x)v(x) \newline
&= u(x)\Delta v(x)+v(x+1)\Delta u(x)
\end{align*}
$$
定义**移位算子**$Ef(x)=f(x+1)$，得到以下法则
$$
\Delta(uv)=u\Delta v +Ev\Delta u=v\Delta u+Eu\Delta v,
$$
于是得到分部求和法则
$$
\sum u\Delta v=uv-\sum Ev\Delta u.
$$
例子：
$$
\begin{align}
\sum\_{0\leqslant k<n} &= \sum\_0^n xH\_x\delta x=\sum\_0^n H\_x \Delta(\frac{x^{\underline{2}}}{2}) \newline
&= \left.\frac{x^{\underline{2}}}{2}H\_x\right|\_0^n -\sum\_0^n \frac{(x+1)^{\underline{2}}}{2}x^{\underline{-1}}\delta x \newline
&= \frac{n^{\underline{2}}}{2}H\_n-\frac12\sum\_0^nx^{\underline{1}}\delta x \newline
&= \frac{n^{\underline{2}}}{2}H\_n-\frac{n^\underline{2}}{4}=\frac{n^{\underline{2}}}{2}\left(H\_n-\frac12\right).
\end{align}
$$

