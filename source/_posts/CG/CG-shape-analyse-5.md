---
title: 第5章 求交问题
category_bar: true
math: true
tags: [求交, 几何]
category: [CG, 《计算机辅助设计与制造中的外形分析》读书笔记]
date: 2025-06-23 15:28:18
---

# 求交问题概述

求交问题可以分为以下几类：
1. 点/点求交（P/P）
2. 点/曲线求交（P/C）
3. 点/曲面求交（P/S）
4. 曲线/曲线求交（C/C）
5. 曲线/曲面求交（C/S）
6. 曲面/曲面求交（S/S）

求交问题通常可以归纳为求解非线性方程组。求交问题所考虑的曲线和曲面可以分为以下几类：
- 有理多项式参数型（RPP，Rational polynomial parametric）
- 过程参数型（PP，Procedure parametric）
- 隐式代数型（IA，Implicit algebraic）
- 隐式过程型（IP，Implicit procedure）

过程曲线曲面是通过算法定义的，通常不能直接利用其解析性质，如等距线、渐屈线和等距面、渐屈面、过渡面、广义柱面。在一定条件下，某些过程曲线和曲面可以表示为RPP型或IA型。

# 求交问题的分类
## 基于维数的分类
1. P/P, P/C, P/S
2. C/C, C/S
3. S/S

## 基于几何类型的分类
### 点的表示形式
1. 显式形式：$\symbfit{r}\_0=(x\_0,y\_0,z\_0)^{\mathrm{T}}$。
2. 过程形式：两个过程曲线、一个过程曲线和一个过程曲面或三个过程曲面的交点。
3. 隐式代数形式：三个隐式曲面的交点，或表示为$f(\symbfit{r})=g(\symbfit{r})=h(\symbfit{r})=0$，其中$f,g,h$为多项式函数，$\symbfit{r}=(x,y,z)^{\mathrm{T}}$。
###  曲线的表示形式
1. 参数形式：$\symbfit{r}=\symbfit{r}(t),\,0\leqslant t\leqslant 1$。
    1. （有理）（分片）多项式曲线：Bézier曲线、有理Bézier曲线、B-样条曲线、NURBS曲线。
    2. 过程曲线：等距线、渐屈线等。
2. 隐式代数形式：平面二维曲线定义为$z=0,f(x,y)=0$；空间三维曲线定义为两个隐式代数曲面的交线$f(\symbfit{r})=g(\symbfit{r})=0$。
### 曲面的表示形式
1. 参数形式：$\symbfit{r}=\symbfit{r}(u,v),\,0\leqslant u,v\leqslant 1$。
    1. （有理）（分片）多项式曲面：Bézier曲面、有理Bézier曲面、B-样条曲面、NURBS曲面。
    2. 过程曲面：等距面、过渡面、广义柱面等。
2. 隐式代数形式：$f(\symbfit{r})=0$。

## 基于计算机中数字表示方法的分类
1. 有理数，$m/n,n\neq 0,m,n\in \mathbb{Z}$。
2. 计算机中的浮点数（FP），是有理数的子集。
3. 代数数（整系数多项式方程的根）。
4. 实数（超越数、三角函数数等）。
5. 区间数，$[a,b],a,b\in\mathbb{R}$。
6. 舍入区间数，$[c,d]$，其中$c,d$为浮点数。

# 点/点求交
点/点求交问题可以简化为检查两点间$\symbfit{r}\_1$和$\symbfit{r}\_2$间的欧氏距离问题，即
$$|\symbfit{r}\_1-\symbfit{r}\_2|<\varepsilon$$
其中$\varepsilon$表示最大允许公差（maximum allowable tolerance）。但要注意传递性问题，例如下图中，$|\symbfit{r}\_1-\symbfit{r}\_2|<\varepsilon$，所以$\symbfit{r}\_1=\symbfit{r}\_2$；$|\symbfit{r}\_2-\symbfit{r}\_3|<\varepsilon$，所以$\symbfit{r}\_2=\symbfit{r}\_3$；但是$|\symbfit{r}\_1-\symbfit{r}\_3|>\varepsilon$，所以$\symbfit{r}\_1\neq \symbfit{r}\_3$。

![在公差范围内，点的相交不具有传递性](img/CG/shapeanalyse/5-point-point.png)
当点是通过过程方式或隐式代数方程定义时，P/P求交问题就可以简化为包含孤立点的区间比较问题。一种定义区间点相等的方式：假设所考虑的区间都很小，如果表示点的两个区间相交，那么认为这两个点相等，并用一个新的区间点来表示这两个重合的点（平行于坐标平面的最小矩形包围盒）。采用这种构造方法，可以保证区间实体造型中的影响范围的正确传递，但代价是降低精度。

# 点/曲线求交
## 点/隐式代数曲线求交
交点定义为：
$$r\_0 \cap \{ z = 0, f(x,y) = 0 \}$$
其中，$f(x,y)=0$通常是多项式，$f(x,y)=0$表示代数曲线。在精确运算环境中，可以直接代入$\symbfit{r}\_0$判断结果是否为0。但对于浮点运算存在舍入误差导致结果不精确为0。
考虑点$\symbfit{r}\_0=(x\_0,y\_0)^{\mathrm{T}}$和平面隐式曲线$f(x,y)=0$之间的距离$d=\min|\symbfit{r}-\symbfit{r}\_0|$，其中点$\symbfit{r}=(x,y)^{\mathrm{T}}$满足$f(\symbfit{r})=0$。考虑逼近距离，将$f(x,y)=0$在点$(x\_0,y\_0)$附近进行一阶泰勒展开：
$$f(x,y) = f(x\_0,y\_0) + f\_x(x\_0,y\_0)\Delta x + f\_y(x\_0,y\_0)\Delta y = 0$$
其中$\Delta x=x-x\_0$和$\Delta y=y-y\_0$。根据距离$|\symbfit{r}-\symbfit{r}\_0|$的稳定性条件，可得如下正交条件：
$$f\_y(x,y)\Delta x - f\_x(x,y)\Delta y = 0$$
由于不知道构成最小距离的脚点（footpoint）$(x,y)$在隐式曲线上的位置，所以再进行如下一阶泰勒展开：
$$\begin{gather*}
f\_x(x,y) = f\_x(x\_0,y\_0) + f\_{xx}(x\_0,y\_0)\Delta x + f\_{xy}(x\_0,y\_0)\Delta y\newline
f\_y(x,y) = f\_y(x\_0,y\_0) + f\_{yx}(x\_0,y\_0)\Delta x + f\_{yy}(x\_0,y\_0)\Delta y
\end{gather*}$$
代入前述公式并忽略二阶项可得：
$$f\_y(x\_0,y\_0)\Delta x-f\_x(x\_0,y\_0)\Delta y=0$$
该式和前面的$f(x,y)$泰勒展开形成一个线性方程组，解得：
$$\Delta x = -\frac{f(x\_0, y\_0)f\_x(x\_0, y\_0)}{f\_x^2(x\_0, y\_0) + f\_y^2(x\_0, y\_0)}, \quad \Delta y = -\frac{f(x\_0, y\_0)f\_y(x\_0, y\_0)}{f\_x^2(x\_0, y\_0) + f\_y^2(x\_0, y\_0)}$$
上述公式中分母不为0。因此，**真实几何距离的一阶逼近**可以简化为：
$$
d=\sqrt{(\Delta x)^2+(\Delta y)^2}=\frac{|f(x\_0,y\_0)|}{|\nabla f(x\_0,y\_0)|}
$$
上述公式中$|\nabla f(x\_0,y\_0)|\neq 0$。因此，如果代数距离满足$\vert\nabla f(x\_{0},y\_{0})\vert<\varepsilon\ll1$，其中$\varepsilon$是小的正常数；并且在包括$\symbfit{r}\_{0}$的感兴趣区域内函数$f$规范化为$\vert f(x,y)\vert\leqslant 1$，那么该非代数距离公式的计算结果可以作为近似最近距离。

考虑如下问题：判断两条交成一个小角度并且交于点$\symbfit{r}\_0$的代数曲线是否相交
$$r\_{0} \cap \{ z = 0, f(x,y) = g(x,y) = 0 \}$$
其中
$$\left|\frac{\nabla f}{|\nabla f|} \cdot \frac{\nabla g}{|\nabla g|}\right| \approx 1$$

![用直线逼近代数曲线](img/CG/shapeanalyse/5-line-curve.png)
对于点$\symbfit{r}\_0$和曲线交点之间距离第一个更好的估计标准为：
$$\begin{gather}
\varphi \approx \arccos\left|\frac{\nabla f(x\_0,y\_0)}{\vert\nabla f(x\_0,y\_0)\vert}\cdot\frac{\nabla g(x\_0,y\_0)}{\vert\nabla g(x\_0,y\_0)\vert}\right|\newline
\delta\_3 = \frac{1}{\varphi}\left(\frac{\left|f(x\_0, y\_0)\right|}{\left|\nabla f(x\_0, y\_0)\right|} + \frac{\left|g(x\_0, y\_0)\right|}{\left|\nabla g(x\_0, y\_0)\right|}\right) < \delta \ll 1
\end{gather}$$

## 点/有理多项式参数曲线求交
问题定义为：
$$\symbfit{r}\_0 \cap \symbfit{r} = \symbfit{r}(t) = \left(\frac{X(t)}{W(t)}, \frac{Y(t)}{W(t)}, \frac{Z(t)}{W(t)}\right)^{\mathrm{T}}, 0 \leqslant t \leqslant 1$$其中$X(t)$、$Y(t)$、$Z(t)$和$W(t)$ 为多项式。

### 初等方法
采用牛顿法和拉盖尔(Laguerre)迭代法求解下述每一个非线性多项式方程，并且搜索它们在 $0 \leqslant t \leqslant 1$中的公共实根：
$$x(t) - x\_0 = 0, \ y(t) - y\_0 = 0, \ z(t) - z\_0 = 0$$
理论上，初等方法简单；但实际利用起来，求解过程复杂、效率不高且容易产生数值误差。

### 基于包围盒与剖分法的最小化方法
随着对曲线$\symbfit{r}(t)$的剖分，它的包围盒也在减小，此时可以利用其包围盒来消除一些可以简单地判断出的不相交情形。对于具有控制顶点$(x\_i, y\_i, z\_i)$ 的有理曲线，它的包围盒由下面公式给出：
$$\begin{gather}
\min(x\_i)\leqslant x(t)\leqslant\max(x\_i)\newline
\min(y\_i)\leqslant y(t)\leqslant\max(y\_i)\newline
\min(z\_i)\leqslant z(t)\leqslant\max(z\_i)
\end{gather}$$
为了消除剖分过程中的数值误差，可以采用有理运算。如果不断地剖分曲线直至包围盒很小，然后利用数值方法计算函数$F(t)=\min\lbrace |\symbfit{r\_0}-\symbfit{r}(t)|^2\rbrace,t\in I$，并在上述区间$I$中选取某个值$t$作为交点的初始逼近。如果最小化过程收敛于$t\_0$且$\sqrt{F(t\_0)}< \varepsilon$，那么$t=t\_0$就是所求的解。
### 距离函数法
一种更鲁棒的方法是计算皮肤距离函数$D=D(t)$的稳定点。计算导数$\dot{D}(t)$的零点，并且检查在这些零点附近平方距离是否最小。
### 隐式化法
将曲线的参数方程转化为隐式方程后，再进行求交处理。
下面，考虑两个次数分别为 $m$和$n$的多项式$x = x(t)$和$y = y(t)$，它们的表达式如下：
$$
\begin{gather}
x = a\_m t^m + a\_{m - 1} t^{m - 1} + \cdots + a\_1 t + a\_0 \newline
y = b\_n t^n + b\_{n - 1} t^{n - 1} + \cdots + b\_1 t + b\_0 
\end{gather}
$$

其中，$a\_m \neq 0$且$b\_n \neq 0$。或者等价地：
$$
\begin{gather}
a\_m t^m + a\_{m - 1} t^{m - 1} + \cdots + a\_1 t + a\_0 - x = 0 \newline
b\_n t^n + b\_{n - 1} t^{n - 1} + \cdots + b\_1 t + b\_0 - y = 0
\end{gather}
$$
用适当的单项式乘以公式的两边可以得到一系列辅助方程，这些辅助方程可以改写为如下$(m + n) \times (m + n)$矩阵的形式：
$$
\begin{bmatrix}
a\_0 - x & a\_1 & \cdots & a\_m \newline
& a\_0 - x & a\_1 & \cdots & a\_m \newline
\cdots & \cdots & \cdots & \cdots & \cdots \newline
& & a\_0 - x & a\_1 & \cdots & a\_m \newline
b\_0 - y & b\_1 & \cdots & b\_n \newline
& b\_0 - y & b\_1 & \cdots & b\_n \newline
\cdots & \cdots & \cdots & \cdots & \cdots \newline
& & b\_0 - y & b\_1 & \cdots & b\_n
\end{bmatrix}
\begin{bmatrix}
1 \newline
t \newline
t^2 \newline
\cdots \newline
\cdots \newline
\cdots \newline
t^{m + n - 1}
\end{bmatrix}=\begin{bmatrix}
0 \newline
0 \newline
0 \newline
\cdots \newline
\cdots \newline
\cdots \newline
0
\end{bmatrix}
$$

其中，空白位置对应于零。上述矩阵的行列式$f(x, y)$ 称为两个多项式的**结式**（resultant）。结式为零（即 $f(x, y) = 0$）是多项式有公共根的充要条件。换言之，所有满足 $f(x, y) = 0$的$x$、$y$ 都位于参数曲线上，因此，**$f(x, y) = 0$为参数方程$x = x(t)$，$y = y(t)$ 的隐式形式**。
结式$f(x,y)$关于$x,y$的次数分别为$m,n$。其中最高次项$f^+(x,y)=(-1)^m\left[a^{n/d}\_my^{m/d}-b^{m/d}\_nx^{n/d}  \right]^d$，其中$d=\gcd(m,n)$，是$m$和$n$的最大公因数。因此若$m=n$，则$f^+(x,y)=(-1)^m\left[a\_my-b\_mx\right]^m$，此时曲线的隐式代数表示的最高次数为$m$。
得到交点后需要确定交点$(x\_0,y\_0)$在参数曲线上所对应的参数$t$，该过程称为反演（inversion）。将$x=x\_0$和$y=y\_0$代入上述矩阵方程，只计算比例为$t^p/t^{p-1}(1\leqslant p\leqslant m + n-1)$的行所形成的方程，就可以得到参数$t$。最后再验证$z\_0=z(t\_0)$。

## 点/过程参数曲线求交
问题定义为：
$$\symbfit{r}\_{0} \cap \symbfit{r}(t),\ 0 \leqslant t \leqslant 1$$
一般不存在易于计算且随剖分逐渐缩小的凸包围盒。逼近求解的方法计算
$$F(t)=|\symbfit{r}(t)-\symbfit{r}\_0|^2,\quad t\in [0,1]$$
还包括检查端点，判断$F(0)$和$F(1)$是否为0。对于一些特殊的过程曲线，例如包含多项式根的有理曲线的等距线和渐屈线，可以采用4.5节中的辅助变量法简化为非线性多项式方程组，并使用IPP方法求解。有些过程曲线可以进行有理参数化，使用前述方法求解。

# 点/曲面求交
## 点/隐式代数曲面求交
点与隐式曲面$f(\symbfit{r}) = 0$求交的数学描述为：
$$\symbfit{r}\_0 \cap f(\symbfit{r}) = 0$$类似于点与隐式曲线求交情形，交点的判别条件为：
$$|f(\symbfit{r}\_0)| < \varepsilon, \frac{|f(\symbfit{r}\_0)|}{|\nabla f(\symbfit{r}\_0)|} < \delta$$
其中$\varepsilon$、$\delta$为很小的正常数，$|\nabla f(\symbfit{r}\_0)| \neq 0$。
## 点/有理多项式参数曲面求交
定义为：
$$\symbfit{r}\_{0} \cap \symbfit{r} = \symbfit{r}(u, v) = \left( \frac{X(u, v)}{W(u, v)}, \frac{Y(u, v)}{W(u, v)}, \frac{Z(u, v)}{W(u, v)} \right)^{\mathrm{T}}, \quad 0 \leqslant u, \ v \leqslant 1$$
### 隐式化方法
对于高阶曲面，存在计算量大且需要采用有理运算以保证稳定性的问题。对于在$u$、$v$方向上最高次数分别为 $m$、$n$的有理多项式曲面，即$\symbfit{r} = \symbfit{r}(u^m, v^n)$，它的隐式方程形式为 $f(\symbfit{r}) = 0$，其中多项式 $f(\symbfit{r})$的最高次数$q \leqslant 2mn$。隐式化方法对于某些特殊的曲线是有效的，例如圆柱和圆锥直纹面、旋转面等。
### 牛顿法
采用RPP曲面的包围盒，结合一定程度的剖分，得到一个初始的逼近解，然后用牛顿法解方程组$x\_0=x(u,v)$、$y\_0=y(u,v)$，并验证结果是否满足$z\_0=z(u,v)$。
### 基于包围盒与剖分法的最小化方法
### 距离函数法

## 点/过程参数曲面求交
典型解法是最小化，通常只能用稠密采样的方法来寻找初始逼近。对于某些过程曲面，如含有多项式根的有理曲面的等距面和渐屈面等，可以采用辅助变量法消去公式中的根号并用IPP算法求解。如果某些过程曲面可以进行有理参数化（如等距面、管状曲面等）可以简化为有理多项式参数曲面的形式。

# 曲线/曲线求交
## 有理多项式参数/隐式代数曲线求交
### 二维平面情形
定义为：
$$\symbfit{r} = \symbfit{r}(t) = \left(\frac{X(t)}{W(t)}, \frac{Y(t)}{W(t)}\right)^{\mathrm{T}} \cap f(x, y) = 0, \quad 0 \leqslant t \leqslant 1$$
记RPP曲线的次数为$n$，隐式代数曲线的全次数为$m$，表达式为：
$$f(x,y)=\sum\_{i=0}^m\sum\_{j=0}^{m-i}c\_{ij}x^iy^j$$
将$x=\frac{X(t)}{W(t)}$和$y=\frac{Y(t)}{W(t)}$代入隐式方程，并乘以$W^m(t)$得到$mn$次多项式：
$$F(t) = \sum\_{i = 0}^{m} \sum\_{j = 0}^{m - i} c\_{ij} X^{i}(t) Y^{j}(t) W^{m - i - j}(t) = \sum\_{i = 0}^{mn} a\_{i} t^{i} = 0$$
这样求交问题就等价于计算$F(t)$在$0\leqslant t\leqslant 1$之间的实根。其系数可以通过符号计算程序实现代入和合并同类项，再使用精确有理运算来数值求解系数。接下来在求解零点时，采用投影多面体算法，将$F(t)$表示为Bernstein基函数形式，实根计算具有更好的数值稳定性。
### 三维空间曲线
定义为：
$$\symbfit{r} = \symbfit{r}(t) = \left( \frac{X(t)}{W(t)}, \frac{Y(t)}{W(t)}, \frac{Z(t)}{W(t)} \right)^{\mathrm{T}} \cap f(\symbfit{r}) = g(\symbfit{r}) = 0, \quad 0 \leqslant t \leqslant 1$$
如果记曲面$f$和$g$的全次数为$m$，将 $x = \frac{X(t)}{W(t)}$，$y = \frac{Y(t)}{W(t)}$和$z = \frac{Z(t)}{W(t)}$代入隐式形式$f(x, y, z) = 0$和$g(x,y,z)=0$，并且两边同乘以$W^m(t)$，可以得到两个单变量非线性多项式方程$F\_1(t)=0$和$F\_2(t)=0$。
一种解法是计算$F\_1=0$和$F\_2=0$的结式，要求两个多项式的所有系数一致。如果结式$R(F\_1(t),F\_2(t))\equiv 0$成立，那么两个多项式存在一个公共根，可以用反演法求得$t$。
另一种鲁棒方法是采用IPP算法，此时代入过程必须是在精确运算环境下实现。
还可以采用IPP算法直接求解具有四个变量$(x,y,z,t)$的过约束方程组：
$$\begin{gather*}
X(t)-xW(t)=0\newline
Y(t)-yW(t)=0\newline
Z(t)-zW(t)=0\newline
f(x,y,z)=0\newline
g(x,y,z)=0
\end{gather*}$$
## 有理多项式参数曲线/有理多项式参数曲线求交
$$
\begin{align}&\symbfit{r} = \symbfit{r}\_1(t) = \left( \frac{X\_1(t)}{W\_1(t)}, \frac{Y\_1(t)}{W\_1(t)}, \frac{Z\_1(t)}{W\_1(t)} \right)^{\mathrm{T}}, \quad 0 \leqslant t \leqslant 1 \newline
\cap &\symbfit{r} = \symbfit{r}\_2(\sigma) = \left( \frac{X\_2(\sigma)}{W\_2(\sigma)}, \frac{Y\_2(\sigma)}{W\_2(\sigma)}, \frac{Z\_2(\sigma)}{W\_2(\sigma)} \right)^{\mathrm{T}}, \quad 0 \leqslant \sigma \leqslant 1
\end{align}$$
设$\symbfit{r}\_1(t)=\symbfit{r}\_2(\sigma)$可以得到关于两个未知数的三个非线性多项式方程（过约束方程组），有以下几种解法。
### 基于包围盒和剖分的最小化方法
如果两个包围盒相交且包围盒大小有限，可以用线性逼近的方法求根。如果两条曲线相切，可能出现包围盒相交但线性逼近不相交的情况。 
包围楔（Bounding Wedge）：一个包围速端曲线的扇形，含有Bézier曲线的所有切向量。包围楔可以用来预测两条曲线的交点个数。
结合包围盒剖分技术和包围楔技术，可以更有效地确定交点，但对于相切的曲线会导致无限剖分。
### 选择两个方程求解$t$、$\sigma$，再代入第三个方程验证。
首先隐式化$x=\frac{X\_2(\sigma)}{W\_2(\sigma)}$和$y=\frac{Y\_2(\sigma)}{W\_2(\sigma)}$得到$f(x,y)=0$，然后将$x=\frac{x\_1(t)}{W\_1(t)}$和$y=\frac{Y\_1(t)}{W\_1(t)}$代入$f(x,y)=0$得到$F(t)=0$，求解方程在$[0,1]$之间的实根，并用反演算法得到$\sigma$。最后判断$\frac{Z\_1(t)}{W\_1(t)}$和$\frac{Z\_2(\sigma)}{W\_2(\sigma)}$是否相等来验证解。为了保证鲁棒性，需要在有理运算条件下进行。
### 遗传算法
首先在每条曲线上随机选取一些点，然后从每条曲线上各选一个点以形成点对。最后用遗传算法生成距离最小的点对。
### IPP算法
直接求解具有两个未知量和三个方程的过约束方程组，该算法保证了鲁棒性。如果参数曲线是整数/有理B-样条形式，那么首先剖分成分段整数/有理Bézier曲线。
## 有理多项式参数曲线/过程参数曲线和过程参数曲线/过程参数曲线的求交问题
定义为：
$$
\symbfit{r}=\symbfit{r}\_1(t)\cap \symbfit{r}=\symbfit{r}\_2(\sigma),\quad 0\leqslant t,\sigma\leqslant 1
$$
采用数值方法计算RPP/PP曲线之间或PP曲线之间的平方距离$D$的最小值：
$$
D=F(t,\sigma)=|\symbfit{r}\_1(t)-\symbfit{r}\_2(\sigma)|^2,\quad 0\leqslant t,\sigma\leqslant 1
$$
在采用数值方法时，$\symbfit{r}\_{1}(t)$和$\symbfit{r}\_{2}(\sigma)$的线性逼近可以作为求解的初始逼近。这种方法通常不能保证得到所有的稳定点。
对于有理多项式曲线的等距线和渐屈线，可以通过辅助变量法来避免多项式的平方根，然后采用IPP算法求解以保证方法的鲁棒性。
## 过程参数曲线/隐式代数曲线求交
定义为：

$$\symbfit{r} = \symbfit{r}(t) \cap f(\symbfit{r}) = g(\symbfit{r}) = 0, \, 0 \leqslant t \leqslant 1$$它可以简化为 PP 曲线与 IA 曲面求交问题，即$\symbfit{r} = \symbfit{r}(t) \cap f(\symbfit{r}) = 0$和$\symbfit{r} = \symbfit{r}(t) \cap g(\symbfit{r}) = 0$，然后比较所得到的解。
## 隐式代数曲线/隐式代数曲线求交
对于平面隐式代数曲线，问题定义为：
$$f(u, v) = 0 \cap g(u, v) = 0, \quad 0 \leqslant u,v \leqslant 1$$
### 隐式化
通过消去$v$得到结式$F(u)$，求解$F(u)=0$得到$u$，再用反演算法得到$v$。不保证鲁棒性。
### 牛顿法
也不保证鲁棒性。
### 区间投影多面体求解法（IPP）
更鲁棒和高效。

# 曲线/曲面求交

## 有理多项式参数曲线/隐式代数曲面求交
定义为：
$$\symbfit{r} = \symbfit{r}(t) = \left(\frac{X(t)}{W(t)}, \frac{Y(t)}{W(t)}, \frac{Z(t)}{W(t)}\right)^{\mathrm{T}} \cap f(\symbfit{r}) = 0, \quad 0 \leqslant t \leqslant 1$$
考虑如下全次数为$m$的隐式代数曲面：
$$ f(x,y,z)=\sum\_{i=0}^m\sum\_{j=0}^{m-i}\sum\_{k=0}^{m-i-j}c\_{ijk}x^iy^jz^k=0 $$
将$n$次有理多项式$x=\frac{X(t)}{W(t)}$，$y=\frac{Y(t)}{W(t)}$和$z=\frac{Z(t)}{W(t)}$代入上述隐式方程并且乘以$W^m(t)$可以得到关于$t$且次数$\leqslant mn$的多项式方程：
$$F(t) = \sum\_{i = 0}^{m} \sum\_{j = 0}^{m - i} \sum\_{k = 0}^{m - i - j} c\_{ijk} X^i(t) Y^j(t) Z^k(t) W^{m - i - j - k}(t) = 0$$

另一种解法是：形成具有四个未知量$(x,y,z,t)$的四个方程，用IPP算法求解。
## 有理多项式参数曲线/有理多项式参数曲面求交
定义为：
$$\begin{gather}
\symbfit{r} = \symbfit{r}\_1(t) = \left( \frac{X\_1(t)}{W\_1(t)}, \frac{Y\_1(t)}{W\_1(t)}, \frac{Z\_1(t)}{W\_1(t)} \right)^{\mathrm{T}}, \quad 0 \leqslant t \leqslant 1 \newline
\cap \symbfit{r} = \symbfit{r}\_2(u, v) = \left( \frac{X\_2(u, v)}{W\_2(u, v)}, \frac{Y\_2(u, v)}{W\_2(u, v)}, \frac{Z\_2(u, v)}{W\_2(u, v)} \right)^{\mathrm{T}}, \quad 0 \leqslant u, v \leqslant 1
\end{gather}$$
上述方程是包含三个未知量$t,u,v$的三个非线性方程，可以通过检测包围盒的方式快速排除没有交点的情形。
### 隐式化方法
隐式化$\symbfit{r}\_2(u,v)$，建议只用于低次数曲面。
### 基于包围盒和剖分的最小化方法
不能保证鲁棒性。
### 区间投影多面体求解法
鲁棒和高效。
## 有理多项式参数/过程参数曲线与过程参数曲面求交
定义为：
$$\symbfit{r} = \symbfit{r}\_1(t) \cap \symbfit{r} = \symbfit{r}\_2(u, v), \quad 0 \leqslant t, u, v \leqslant 1$$可以简化为具有三个未知量的三个非线性（包括非多项式函数）方程，但是非多项式函数的边界通常难以计算。
这类问题的一种求解方法是最小化方法：$$F(t, u, v) = \left| \symbfit{r}\_1(t) - \symbfit{r}\_2(u, v) \right|^2$$其中定义域为$0 \leqslant t, u, v \leqslant 1$。
## 过程参数曲线/隐式代数曲面求交
定义为：
$$
\symbfit{r}=\symbfit{r}(t)\cap f(\symbfit{r})=0,\quad 0\leqslant t\leqslant 1
$$
这样可以得到具有四个未知量的四个方程。可以使用$\symbfit{r}=\symbfit{r}(t)$的线性逼近作为牛顿法的初值，但没有鲁棒性保证。
## 隐式代数曲线/隐式代数曲面求交
定义为：
$$\underbrace{f(\symbfit{r}) = g(\symbfit{r})}\_{\text{曲线}} = \underbrace{h(\symbfit{r})}\_{\text{曲面}} = 0$$
求解方法包括：消去法、牛顿法、目标函数$F(\symbfit{r})=f^2+g^2+h^2$的最小化法、用线性样条逼近$f(\symbfit{r}) = g(\symbfit{r}) = 0$并采用最小化逐步求精的方法、IPP算法等。、
## 隐式代数曲线/有理多项式参数曲面求交
定义为：
$$f(\symbfit{r}) = g(\symbfit{r}) = 0 \cap \symbfit{r} = \symbfit{r}(u, v) = \left( \frac{X(u, v)}{W(u, v)}, \frac{Y(u, v)}{W(u, v)}, \frac{Z(u, v)}{W(u, v)} \right)^{\mathrm{T}},\, 0\leqslant u,v\leqslant1$$
将$\symbfit{r}=\symbfit{r}(u,v)$代入$f(\symbfit{r}) = 0$和$g(\symbfit{r}) = 0$，可以得到两条代数曲线$F(u,v)=0$和$G(u,v)=0$。这样，问题就转化成了上文中的IA/IA。

# 曲面/曲面求交
## 有理多项式参数曲面/隐式代数曲面求交
定义为：
$$\symbfit{r} = \symbfit{r}(u, v) = \left( \frac{X(u, v)}{W(u, v)}, \frac{Y(u, v)}{W(u, v)}, \frac{Z(u, v)}{W(u, v)} \right)^{\mathrm{T}} \cap f(\symbfit{r}) = 0, \quad 0 \leqslant u,v \leqslant 1$$
可以看作具有5个未知量的四个代数方程（欠约束）。对于常用的低阶曲面，可以将$\symbfit{r}(u,v)$代入$f(\symbfit{r})=0$，从而得到关于$u,v$的隐式代数曲线。常见的低阶隐式代数曲面有：平面（一次）、自然二次曲面（柱面、球面、锥面）和圆环面（四次）。调查表明：表示机械零件的曲面中90%是低阶代数曲面。同时低阶隐式代数曲面可以进行低阶有理多项式参数化。所以两个低阶隐式曲面求交可以转化为本节的情形。

### 问题表述
设全次数为$m$的隐式代数曲面$f(x,y,z)=0$表达式为：
$$
f(x,y,z)=\sum\_{i=0}^{m}\sum\_{j=0}^{m-i}\sum\_{k=0}^{m-i-j}c\_{ijk}x^iy^jz^k
$$
将 $x = \frac{X(u, v)}{W(u, v)}$，$y = \frac{Y(u, v)}{W(u, v)}$，$z = \frac{Z(u, v)}{W(u, v)}$ 代入上述方程，其中$X,Y,Z,W$关于$u$和$v$的最高次数分别为$p$和$q$，并乘以$W^m(u,v)$可得代数曲线如下：
$$
F(u,v)=\sum\_{i=0}^{m}\sum\_{j=0}^{m-i}\sum\_{k=0}^{m-i-j}c\_{ijk}X^i(u,v)Y^j(u,v)Z^k(u,v)W^{m-i-j-k}(u,v)=0
$$
代数曲线关于$u$和$v$的最高次数分别为$mp$和$mq$。这样求交问题就转化为跟踪代数曲线$F(u,v)=0$。曲线跟踪要求不能忽略任何特征，如小环、奇异点等，并能精确计算所有分支。
代数曲线$F(u,v)=\sum\_{i=0}^{M}\sum\_{j=0}^Nc\_{ij}^Mu^iv^j=0$可以重新表示为Bernstein多项式的形式如下：
$$
F(u,v)=\sum\_{i=0}^M\sum\_{j=0}^Nc\_{ij}^BB\_{i,M}(u)B\_{j,N}(v)=0
$$
其中$(u,v)\in [0,1]^2$。
如果对于所有$i,j$，$c\_{ij}^B>0$或$c\_{ij}^B<0$成立，两个曲面不相交。当所有$c\_{ij}^B=0$时，两个曲面完全重合。
![一个稍复杂的交线结果例子](img/CG/shapeanalyse/5-complex-intcurve.png)

### 跟踪法
首先给出代数曲线每一个分支上的一个点，根据曲线的微分性质跟踪曲线。思想是根据曲线$F(u,v)=0$求得增量$\delta u$和$\delta v$，使其满足$F(u+\delta u,v+\delta v)=0$。
泰勒展开：
$$
F(u + \delta u, v + \delta v) = F(u, v) + F\_{u}\delta u + F\_{v}\delta v+ \frac{1}{2}(F\_{uu}\delta u^{2} + 2F\_{uv}\delta u\delta v + F\_{vv}\delta v^{2}) + \cdots
$$
当$F\_u$和$F\_v$不全为0时，一阶逼近为：$F\_u\delta u+F\_v\delta v=0$，解得：
$$
\delta v\_L=-\frac{F\_u}{F\_v}\delta u
$$
其中假设$F\_v\neq 0$。
![代数曲线在点$(u,v)$处的放大图](img/CG/shapeanalyse/5-local-uv.png)
如上图所示，$\delta v\_L$的值可能导致点$Q$远离曲线，此时可以使用具有初始逼近$v\_1=v+\delta v\_L$的牛顿法计算$F(u+\delta u,v)=0$，从而获得较高精度的$\delta v$。对于垂直的分支，即$|F\_v|$很小的情形，可以采用公式$\delta u\_L=-\frac{F\_v}{F\_u}\delta v$。
为了避免分情况讨论，也可以改写为：
$$ F\_u\dot{u}+F\_v\dot{v}=0 $$
其中$u,v$看作参数$t$的函数，上述微分方程的解为：
$$
\begin{gather}
\dot{u}=\xi F\_v(u,v)\newline
\dot{v}=-\xi F\_u(u,v)
\end{gather}
$$
其中$\xi$为任意非零因子。可以选择$\xi$使得曲线为弧长参数化：
$$
\xi  = \pm \frac{1}{\sqrt{EF\_v^2-2FF\_uF\_v+GF\_u^2}}
$$
其中$E,F,G$是曲面在$(u,v)$处的第一基本齐式系数。于是对于上述一阶非线性微分方程组，可以采用自适应步长的龙格库塔方法等求解。
为了使跟踪法能够正确工作，必须事先给出所有分支的起点。在$F\_u^2+F\_v^2$很小的地方，太大的步长可能会导致跟踪偏离或循环，此时需要缩短步长。
### 特征点方法
通过寻找如下特征点，确定曲线跟踪的起始点。
#### 边界点（border points）
$F(u,v)=0$与参数空间$[0,1]^2$四条边界的交点。
#### 拐点（turning points）
**$u$拐点**是指曲线$F(u,v)=0$上切向平行于$u=0$轴的点，同时满足$F=F\_v=0$和$F\_u\neq 0$；**$v$拐点**是指曲线上切向平行于$v=0$轴的点，同时满足$F=F\_u=0$和$F\_v\neq 0$。如果$F$关于$(u,v)$的次数为$(M,N)$，那么$F\_u$和$F\_v$的次数分别为$(M-1,N)$和$(M,N-1)$。在整个复平面（原文如此，这里应该是指$\mathbb{C}^2$）上，$u$拐点和$v$拐点的数目分别为$2MN-M$和$2MN-N$。在实际应用中，位于$[0,1]^2$内的拐点数目要少得多。
#### 奇异点（singular points）
曲线上同时满足三个方程$F=F\_u=F\_v=0$的点成为奇异点。注意到$f(x,y,z)=0$和$F(u,v)=W^m(u,v)f(x,y,z)$，有：
$$
F\_u=mW^{m-1}W\_uf+W^m \frac{\partial f}{\partial u}=W^m \nabla f\cdot \symbfit{r}\_u
$$
类似地，$F\_v=W^m\nabla f\cdot \symbfit{r}\_v$，因此在奇异点处$\nabla f\cdot \symbfit{r}\_u=\nabla f\cdot \symbfit{r}\_v=0$，则$\nabla f\parallel \symbfit{r}\_u\times\symbfit{r}\_v$。这表示**两个曲面的法向在奇异点处平行，即两个曲面在奇异点处相切**。在整个复平面上，奇异点的个数至多为$2MN-M-N+1$，限制在$[0,1]^2$内的奇异点少得多。

| $S\_1$     | $S\_2$     | 次数为$M$、$N$的代数曲线$F(u, v)$ | $u$拐点的最大数目$2MN - M$ | $v$拐点的最大数目$2MN - N$ | 奇异点的最大数目$2MN - M - N + 1$ |
| ---------- | ---------- | --------------------------------- | -------------------------- | -------------------------- | --------------------------------- |
| 平面       | 双二次曲面 | $2, 2$                            | $6$                        | $6$                        | $5$                               |
| 平面       | 双三次曲面 | $3, 3$                            | $15$                       | $15$                       | $13$                              |
| 二次曲面   | 双二次曲面 | $4, 4$                            | $28$                       | $28$                       | $25$                              |
| 二次曲面   | 双三次曲面 | $6, 6$                            | $66$                       | $66$                       | $61$                              |
| 圆环面     | 双二次曲面 | $8, 8$                            | $120$                      | $120$                      | $113$                             |
| 圆环面     | 双三次曲面 | $12, 12$                          | $276$                      | $276$                      | $265$                             |
| 双二次曲面 | 双二次曲面 | $16, 16$                          | $496$                      | $496$                      | $481$                             |
| 双三次曲面 | 双二次曲面 | $36, 36$                          | $2556$                     | $2556$                     | $2521$                            |
| 双三次曲面 | 双三次曲面 | $54, 54$                          | $5778$                     | $5778$                     | $5725$                            |

#### 奇异点的分析
考虑过代数曲线$F(u,v)=0$上点$(u\_0,v\_0)$的直线$L$的参数方程：
$$ u=u\_0+\alpha t,\quad v=v\_0+\beta t $$
通过计算$F(u\_0+\alpha t,v\_0+\beta t)=0$的根，可以得到直线与曲线的交点。在点$(u\_0,v\_0)$处泰勒展开得：
$$ (\alpha F\_u+\beta F\_v)t+\frac12(\alpha^2F\_{uu}+2\alpha \beta F\_{uv}+\beta^2 F\_{vv})t^2+\cdots = 0
$$
当$F\_u$和$F\_v$不都为0时，方程只有一个单根$t=0$，并且在除 $\alpha F\_{u} + \beta F\_{v} = 0$ 之外（$\alpha$、$\beta$取某些特殊值），过$(u\_{0}, v\_{0})$点的直线与代数曲线在$(u\_{0}, v\_{0})$处只有一个简单交点。在$\alpha F\_{u} + \beta F\_{v} = 0$处，如果$F$ 至少有一个二阶偏导数不为零（$F\_{uu}^{2} + F\_{uv}^{2} + F\_{vv}^{2} > 0$），公式有一个二重根 $t = 0$，此时，$L$与代数曲线在$(u\_{0}, v\_{0})$ 点处相切。
当$(u\_0, v\_0)$是奇异点时（$F(u, v) = F\_u(u, v) = F\_v(u, v) = 0$），并且$F\_{uu}$、$F\_{uv}$和$F\_{vv}$至少一个不为零（$F\_{uu}^2 + F\_{uv}^2 + F\_{vv}^2 > 0$），那么$t = 0$是一个二重根。此时除满足如下条件的$\alpha$和$\beta$以外，直线与代数曲线在$(u\_0, v\_0)$处至少有两个交点：
$$\alpha^2 F\_{uu} + 2\alpha\beta F\_{uv} + \beta^2 F\_{vv} = 0$$
当$\alpha$和$\beta$满足上式时，如果至少一个三阶偏导数不为零（$F\_{uuu}^2 + F\_{uuv}^2 + F\_{uvv}^2 + F\_{vvv}^2 > 0$），那么$t = 0$是一个三重根。此时可以通过上式解$\alpha/\beta$或者$\beta/\alpha$，结果会有如下三种可能：
1. 两个不同的实根：它们对应于奇异点处两个不同的切向，表明代数曲线存在自相交。
2. 一个二重实根：这个根对应于一个切向，表明代数曲线的奇异点是个尖点。
3. 两个复根：在奇异点处没有实的相切点表明一个孤立点。

#### 计算所有分支的起始点
起始点可以是边界点、拐点和奇异点。边界点可以通过求解一个单变量多项式方程得到，例如沿着$u=0$的边界点可以如下计算：
$$ F(0,v)=\sum\_{j=0}^Nc\_{0j}^B B\_{j,N}(v)=0$$
拐点和奇异点的计算包括一阶偏导数：
$$\begin{gather}
F\_{u}(u, v) = M \sum\_{i = 0}^{M - 1} \sum\_{j = 0}^{N} \left(c\_{i + 1, j}^{B} - c\_{ij}^{B}\right) B\_{i, M - 1}(u) B\_{j, N}(v) \newline
F\_{v}(u, v) = N \sum\_{i = 0}^{M} \sum\_{j = 0}^{N - 1} \left(c\_{i, j + 1}^{B} - c\_{ij}^{B}\right) B\_{i, M}(u) B\_{j, N - 1}(v)
\end{gather}$$
因此拐点计算（$F=F\_u=0$和$F=F\_v=0$）等价于求解具有两个变量、两个非线性多项式方程组成的方程组，奇异点的计算（$F=F\_u=F\_v$）等价于求解具有两个变量、三个非线性多项式方程组成的过约束方程组。

## 有理多项式参数曲面/有理多项式参数曲面求交
定义为：
$$\begin{align}
&\symbfit{r} = \symbfit{r}\_1(\sigma, t) = \left( \frac{X\_1(\sigma, t)}{W\_1(\sigma, t)}, \frac{Y\_1(\sigma, t)}{W\_1(\sigma, t)}, \frac{Z\_1(\sigma, t)}{W\_1(\sigma, t)} \right)^{\mathrm{T}}, \quad 0 \leqslant \sigma, \ t \leqslant 1 \newline
\cap &\symbfit{r} = \symbfit{r}\_2(u, v) = \left( \frac{X\_2(u, v)}{W\_2(u, v)}, \frac{Y\_2(u, v)}{W\_2(u, v)}, \frac{Z\_2(u, v)}{W\_2(u, v)} \right)^{\mathrm{T}}, \quad 0 \leqslant u, \ v \leqslant 1
\end{align}$$
设$\symbfit{r}\_1=\symbfit{r}\_2$，可以得到具有四个未知量$\sigma,t,u,v$的三个非线性多项式方程，组成欠约束方程组，可以通过IPP算法求解。
另一种解法是将$\symbfit{r}\_1$隐式化为$f(x,y,z)=0$，并将$x=\frac{X\_2(u,v)}{W\_2(u,v)}$，$y=\frac{Y\_2(u,v)}{W\_2(u,v)}$，$z=\frac{Z\_2(u,v)}{W\_2(u,v)}$代入到$f$中，就简化为曲面次数较低时的情形RPP/IA。
目前主要有三类方法求解RPP/RPP求交问题。
### 网格法
核心思想是降低曲面求交问题的维数。计算曲面上一系列等参数曲线与另一个曲面上一系列等参数曲线的交点，然后将离散交点连接起来形成不同的交线分支。初始网格分辨率选取较重要。若网格太粗，可能会丢掉一些重要特征，如较小的环、孤立点等，还有可能导致曲线交点的错误连接。
### 剖分法
基本思想是将问题递归地分解为更简单的相似问题，直至简化到可以直接求解的程度，然后将各个独立的解连接起来形成完整的解。简单的剖分法采用均匀剖分，这样可以得到均匀四叉树结构。优点是不需要像步进法一样需要起始点。缺点是：在奇异点和奇异点附近，解的分支不能保证正确连接；在逼近解中，可能会丢失一些小环或产生一些额外的环；在用于求解高精度的曲面交线时，会导致数据量急剧增加，求解变慢。
自适应剖分法结合有效的局部技术可以得到较高的精度，是计算特征点的最实用方法，然后可以采用步进法以跟踪所有的交线。
### 步进法
根据局部微分几何性质得到交线上的特定方向，然后按照该方向从交线上一点前进到下一点，从而得到一个交线分支上的一系列点。步进法要求知道每一个连通分量上至少一个起始点，并且已知所有奇异点。对于三维RPP/RPP曲面求交情形，一个可以便于计算所有交线分支的起始点集是：**边界点**和两个曲面间的**共线法向点**。共线法向点计算给出了每个环的一个内部点和所有奇异点。
边界点是指在$\sigma-t$和$u-v$参数域中，参数坐标$\sigma,t,u,v$中至少有一个取得边界值的交点，例如$\symbfit{r}\_1(0,t)=\symbfit{r}\_2(u,v)$。其他方法有迭代优化方法和Moore-Penrose伪逆法。
共线法向点是指两个曲面上法向共线的点，主要用于曲面相交环的检测。设$\symbfit{p}=\symbfit{r}\_1$，$\symbfit{q}=\symbfit{r}\_2$，共线法向点满足如下方程：
$$\begin{gather}
(\symbfit{p}\_{\sigma} \times \symbfit{p}\_{t}) \cdot \symbfit{q}\_{u} = 0, \quad (\symbfit{p}\_{\sigma} \times \symbfit{p}\_{t}) \cdot \symbfit{q}\_{v} = 0 \newline
(\symbfit{p} - \symbfit{q}) \cdot \symbfit{p}\_{\sigma} = 0, \quad (\symbfit{p} - \symbfit{q}) \cdot \symbfit{p}\_{t} = 0
\end{gather}$$
形成由四个非线性方程组成的方程组，可以采用第四章的鲁棒性算法求解。如果沿一个参数方向在共线法向点处剖分曲面，就可以得到所有子区域的边界点Grandline 和 Klein 随后系统地提出了 B-样条曲面求交的拓扑分析方法，该方法首先确定包括封闭环在内的交线拓扑结构，然后采用基于数值积分的微分代数方程组的步进法跟踪出所有交线分支。拓扑分析方法依赖于将投影多面体算法推广至浮点运算环境下的 B-样条曲线（在处理的每一步，第一个子区域上的方程规范化到$[-1,1]$范围，节点向量规范化到$[0,1]$范围。这样就可以充分利用浮点数在该范围内密度大的优势，从而提高算法的数值鲁棒性）。
在进行交线跟踪时，前进的方向与交线$\symbfit{c}(s)$的切向重合，并且该切向与两个曲面的法向垂直。因此前进方向可以通过过如下公式得到：
$$
\symbfit{c}'(s)=\frac{\symbfit{P}(\sigma,t)\times\symbfit{Q}(u,v)}{|\symbfit{P}(\sigma,t)\times\symbfit{Q}(u,v)|}
$$
其中$\symbfit{P}(\sigma,t)=\symbfit{p}\_{\sigma}\times\symbfit{p}\_{t}$，$\symbfit{Q}(u,v)=\symbfit{q}\_u\times\symbfit{q}\_v$是曲面$\symbfit{p}$和$\symbfit{q}$的法向量。上述单位化可以使交线$\symbfit{c}(s)$为弧长参数化。当两个曲面相切时，分母为0，要采用其他前进方向。
曲面的交线也可以看作两个曲面上的曲线。在$\sigma t$参数平面中的一条参数曲线$\sigma = \sigma(s)$、$t = t(s)$定义了参数曲面$\symbfit{p}(\sigma,t)$上一条参数曲线$\symbfit{r} = \symbfit{c}(s) = \symbfit{p}(\sigma(s),t(s))$；同样，在$uv$参数平面中的一条参数曲线$u = u(s)$、$v = v(s)$定义了参数曲面$\symbfit{q}(u,v)$上一条参数曲线$\symbfit{r} = \symbfit{c}(s) = \symbfit{q}(u(s),v(s))$。根据链式法则可以计算出曲面上的曲线的一阶导数：
$$\symbfit{c}'(s) = \symbfit{p}\_{\sigma}\sigma' + \symbfit{p}\_{t}t', \quad \symbfit{c}'(s) = \symbfit{q}\_{u}u' + \symbfit{q}\_{v}v' $$
由于从上文中可以得到交线的单位切向量，通过对上式的第一个方程两边点乘$\symbfit{p}\_{\sigma}$和$\symbfit{p}\_{t}$、对第二个方程两边点乘$\symbfit{q}\_{u}$和$\symbfit{q}\_{v}$，可以得到$\sigma'$和$t'$以及$u'$和$v'$，这样可以得到两个新的线性方程组。该方程组的解为：
$$\begin{gather}
\sigma' = \frac{\det\left( \symbfit{c}', \symbfit{p}\_{t}, \symbfit{P}(\sigma,t) \right)}{\symbfit{P}(\sigma,t) \cdot \symbfit{P}(\sigma,t)}, \quad t' = \frac{\det\left( \symbfit{p}\_{\sigma}, \symbfit{c}', \symbfit{P}(\sigma,t) \right)}{\symbfit{P}(\sigma,t) \cdot \symbfit{P}(\sigma,t)} \newline
u' = \frac{\det\left( \symbfit{c}', \symbfit{q}\_{v}, \symbfit{Q}(u,v) \right)}{\symbfit{Q}(u,v) \cdot \symbfit{Q}(u,v)}, \quad v' = \frac{\det\left( \symbfit{q}\_{u}, \symbfit{c}', \symbfit{Q}(u,v) \right)}{\symbfit{Q}(u,v) \cdot \symbfit{Q}(u,v)}
\end{gather}$$

## 隐式代数曲面/隐式代数曲面求交
定义为：
$$ 
f(\symbfit{r})=0\cap g(\symbfit{r})=0
$$
其中$f,g$为多项式函数。这样可以得到关于三个变量$\symbfit{r}$的两个方程。Bajaj等人提出了一种计算IA/IA曲面交线的步进法，同样适用 于参数曲面。
另一种方法是从$f,g$中消去一个变量例如$z$，从而得到另外两个变量所确定的平面内投影，然后跟踪代数曲线并用反演算法得到变量$z$。

# 曲面和曲面的重叠
**定理** 如果两条$C^{\infty}$曲线段$\symbfit{r}\_1(t)$和$\symbfit{r}\_2(\sigma)$存在部分重叠，那么两条曲线一定完全重叠；否则重叠部分止于曲线边界点。

# 曲线曲面的自相交
平面有理多项式参数曲线自相交问题，可以表示为计算两个不同的参数$t\neq \sigma$，使得：$\symbfit{r}(\sigma)=\symbfit{r}(t)$，或者以分量形式表示为：
$$
\frac{X(\sigma)}{W(\sigma)}-\frac{X(t)}{W(t)}=0,\, \frac{Y(\sigma)}{W(\sigma)}-\frac{Y(t)}{W(t)}=0
$$
其中：
$$\begin{gather}
X(t) = \sum\_{i = 0}^{n} w\_{i}x\_{i}B\_{i,n}(t)\newline
Y(t) = \sum\_{i = 0}^{n} w\_{i}y\_{i}B\_{i,n}(t)\newline
W(t) = \sum\_{i = 0}^{n} w\_{i}B\_{i,n}(t)
\end{gather}$$
**使用IPP算法求解** 将上述两个方程乘以分母，并重写为：
$$\begin{gather}
\sum\_{i = 0}^{n}\sum\_{j = 0}^{n} w\_i w\_j x\_i \left[ B\_{i,n}(\sigma) B\_{j,n}(t) - B\_{j,n}(\sigma) B\_{i,n}(t) \right] = 0  \newline
\sum\_{i = 0}^{n}\sum\_{j = 0}^{n} w\_i w\_j y\_i \left[ B\_{i,n}(\sigma) B\_{j,n}(t) - B\_{j,n}(\sigma) B\_{i,n}(t) \right] = 0
\end{gather}$$
这样形成了关于$t,\sigma$的两个非线性多项式方程组成的方程组。因为：
$$\frac{B\_{i,n}(\sigma)B\_{j,n}(t) - B\_{j,n}(\sigma)B\_{i,n}(t)}{\sigma - t} = B\_{j,n}(t)\frac{B\_{i,n}(\sigma) - B\_{i,n}(t)}{\sigma - t} - B\_{i,n}(t)\frac{B\_{j,n}(\sigma) - B\_{j,n}(t)}{\sigma - t}$$
所以容易从方程组中提取公因子$(\sigma - t)$，从而排除平凡解$\sigma=t$。
有理多项式参数曲面的自相交曲线定义为计算两个不同的参数$(\sigma,t)\neq(u,v)$，使得：$\symbfit{r}(\sigma,t)=\symbfit{r}(u,v)$。
使用IPP算法求解曲面自相交问题效率不高，困难在于如何删除平凡解。

