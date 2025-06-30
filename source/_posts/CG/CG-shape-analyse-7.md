---
title: 第7章 距离函数
category_bar: true
math: true
tags: [几何, 微积分]
category: [CG, 《计算机辅助设计与制造中的外形分析》读书笔记]
date: 2025-06-30 21:50:40
---
# 引言
# 问题表述
## 点集间距离的定义
最小距离问题的一般求解方法：
1. 用函数$D(t)$，$D(u,v)$等表示距离的平方。
2. 求解$\nabla D=0$。
3. 检查在这些零点处是否取到最小值。

**点/线**  三维笛卡尔空间中，点$\symbfit{p}\_0=(x\_0,y\_0,z\_0)^{\mathrm{T}}$到参数曲线$\symbfit{r}(t)$上任意一点的平方距离函数定义为：
$$
D(t)=\left|\symbfit{p}\_0-\symbfit{r}(t)\right|^2=(\symbfit{p\_0}-\symbfit{r}(t))\cdot (\symbfit{p\_0}-\symbfit{r}(t))
$$
$$
D\_t(t)=0\quad \Rightarrow \quad (\symbfit{p}\_0-\symbfit{r}(t))\cdot \symbfit{r}\_t(t)=0
$$
**点/面**  三维笛卡儿空间中，点 $\symbfit{p}\_0 = (x\_0, y\_0, z\_0)^\mathrm{T}$到参数曲面$\symbfit{r} = \symbfit{r}(u, v)$上任意一点的平方距离函数定义为：
$$D(u, v) = |\symbfit{p}\_0 - \symbfit{r}(u, v)|^2 = (\symbfit{p}\_0 - \symbfit{r}(u, v)) \cdot (\symbfit{p}\_0 - \symbfit{r}(u, v))$$
$$
D\_u(u,v)=D\_v(u,v)=0\quad\Rightarrow\quad (\symbfit{p}\_{0} - \symbfit{r}(u, v)) \cdot \symbfit{r}\_{u}(u, v) = (\symbfit{p}\_{0} - \symbfit{r}(u, v)) \cdot \symbfit{r}\_{v}(u, v) = 0
$$
**线/线**  三维笛卡儿空间中，参数曲线$\symbfit{p} = \symbfit{p}(u)$和$\symbfit{q} = \symbfit{q}(v)$之间的平方距离函数定义为：
$$D(u, v) = |\symbfit{p}(u) - \symbfit{q}(v)|^2 = (\symbfit{p}(u) - \symbfit{q}(v)) \cdot (\symbfit{p}(u) - \symbfit{q}(v))$$
$$D\_{u}(u, v) = D\_{v}(u, v) = 0\quad\Rightarrow\quad(\symbfit{p}(u) - \symbfit{q}(v)) \cdot \symbfit{p}\_{u}(v) = (\symbfit{p}(u) - \symbfit{q}(v)) \cdot \symbfit{q}\_{v}(v) = 0$$
**线/面**  三维笛卡儿空间中，参数曲线 $\symbfit{p} = \symbfit{p}(t)$和参数曲面$\symbfit{q} = \symbfit{q}(u, v)$之间的平方距离函数定义为：
$$D(t, u, v) = \left\lvert \symbfit{p}(t) - \symbfit{q}(u, v) \right\rvert^2 = (\symbfit{p}(t) - \symbfit{q}(u, v)) \cdot (\symbfit{p}(t) - \symbfit{q}(u, v))$$
$$\begin{gather}
D\_t(t, u, v) = D\_u(t, u, v) = D\_v(t, u, v) = 0\quad \newline \Rightarrow
(\symbfit{p}(t) - \symbfit{q}(u, v)) \cdot \symbfit{p}\_t(t) = (\symbfit{p}(t) - \symbfit{q}(u, v)) \cdot \symbfit{q}\_u(u, v) 
= (\symbfit{p}(t) - \symbfit{q}(u, v)) \cdot \symbfit{q}\_v(u, v) = 0
\end{gather}$$
**面/面**  三维笛卡儿空间中，参数曲面$\symbfit{p}=\symbfit{p}(\sigma,t)$和$\symbfit{q}=\symbfit{q}(u,v)$之间的平方距离函数定义为：
$$D(\sigma,t,u,v)=\left\vert\symbfit{p}(\sigma,t)-\symbfit{q}(u,v)\right\vert^{2}=(\symbfit{p}(\sigma,t)-\symbfit{q}(u,v))\cdot(\symbfit{p}(\sigma,t)-\symbfit{q}(u,v))$$
$$\begin{align}
&D\_{\sigma}(\sigma, t, u, v) = D\_{t}(\sigma, t, u, v) = D\_{u}(\sigma, t, u, v) = D\_{v}(\sigma, t, u, v) = 0 \newline
\Rightarrow \quad&(\symbfit{p}(\sigma, t) - \symbfit{q}(u, v)) \cdot \symbfit{p}\_{\sigma}(\sigma, t) = (\symbfit{p}(\sigma, t) - \symbfit{q}(u, v)) \cdot \symbfit{p}\_{t}(\sigma, t) = 0 \newline
&(\symbfit{p}(\sigma, t) - \symbfit{q}(u, v)) \cdot \symbfit{q}\_{u}(u, v) = (\symbfit{p}(\sigma, t) - \symbfit{q}(u, v)) \cdot \symbfit{q}\_{v}(u, v) = 0
\end{align}$$
在以上式子中，参数$\sigma,t,u,v$均满足：$0\leqslant \sigma,t,u,v \leqslant 1$。
由于上述距离问题所涉及的曲线和曲面是有界的，所以有必要将距离计算问题分解为几个子问题，它们分别对应于处理几何物体的内部点和边界点。算法的主要步骤如下：
1. 计算在点集内部区域的极小和极大距离；
2. 如果一个点集是曲面，计算在曲面四个顶点处的距离；
3. 如果一个点集是曲面，计算在曲面四条边界曲线上的极小和极大距离；
4. 如果一个点集是曲线，计算在曲线两个端点处的距离；
5. 比较得到的所有距离极值，得到最小和最大值。

## 距离函数稳定点的几何解释
# 关于稳定点的进一步讨论
## 稳定点的分类
### 一元函数
对于单参数问题，假设$u\_0$是$D(u)$的一个稳定点，即$\dot{D}(u\_0)=0$，那么泰勒级数展开为：
$$\begin{align}
D(u\_0+h)-D(u\_0)&=h\dot{D}(u\_0)+\frac{h^2}{2}\ddot{D}(u\_0+ht) \newline
&=\frac{h^2}{2}\ddot{D}(u\_0+ht) \tag{$t\in [0,1]$}
\end{align}$$
当$\ddot{D}(u\_0)>0$时，$u\_0$是极小值点；当$\ddot{D}(u\_0)<0$时，$u\_0$是极大值点。
一般地，如果$D^{(n)}(a)$是第一个不为零的导数，那么函数具有如下性质：
- 如果 $n$为奇数，那么$D(a)$既不是最大点，也不是最小点；
- 如果$n$为偶数：
    - 如果$D^{(n)}(a) < 0$，$D(a)$是最大点；
    - 如果$D^{(n)}(a) > 0$，$D(a)$ 是最小点。

### 二元函数
考虑函数$D(u,v)$，在稳定点$(u\_0,v\_0)$处泰勒展开为：
$$\begin{align*}
&D(u\_0 + h, v\_0 + k) - D(u\_0, v\_0) \newline
= &\frac{h^2}{2} D\_{uu}(u\_0 + ht, v\_0 + kt) + hk D\_{uv}(u\_0 + ht, v\_0 + kt) \newline
&+ \frac{k^2}{2} D\_{vv}(u\_0 + ht, v\_0 + kt) \tag{$t\in[0,1]$}
\end{align*}$$
设$\delta=\begin{vmatrix}D\_{uu} &D\_{uv} \newline D\_{uv} &D\_{vv}\end{vmatrix}=D\_{uu}D\_{vv}-D\_{uv}^2$，函数在稳定点$(u\_0,v\_0)$处的性质如下：
- $\delta > 0$：
    - $D\_{uu}<0$：极大值点；
    - $D\_{uu}>0$：极小值点；
- $\delta <0$：鞍点；
- 如果上述条件都不满足，需要检查高阶导数。

### 多元函数
二次型是具有 $\symbfit{u}^{\mathrm{T}}\symbfit{A}\symbfit{u}$型的表达式，其中$\symbfit{u} = \begin{bmatrix} u\_1 \newline u\_2 \newline \cdots \newline u\_n \end{bmatrix}$是一个具有$n$ 个变量的列向量，$\symbfit{A}$是$n \times n$矩阵。一个二次型称为：
1. 正定的，如果对于所有$\symbfit{u} \neq \symbfit{0}$，有 $\symbfit{u}^{\mathrm{T}}\symbfit{A}\symbfit{u} > 0$；
2. 半正定的，如果对于所有 $\symbfit{u}$，有 $\symbfit{u}^{\mathrm{T}}\symbfit{A}\symbfit{u} \geqslant 0$；
3. 负定的（半负定的），如果 $\symbfit{u}^{\mathrm{T}}\symbfit{A}\symbfit{u}<0$；
4. 不定的，如果是其它情形（例如既取正值，也取负值的二次型）。

多元函数$D(\symbfit{u})$在稳定点$\symbfit{u}\_0$的泰勒展开为：
$$D(\symbfit{u}\_0 + \symbfit{h}) - D(\symbfit{u}\_0) = \frac{1}{2} \symbfit{h}^{\mathrm{T}} \symbfit{H} \symbfit{h} + O(|\symbfit{h}|^3)$$
$\symbfit{H}$是$D$的 Hessian 矩阵，其第$i$行、第$j$列元素为：
$$H\_{ij} = \frac{\partial^2 D}{\partial u\_i \partial u\_j}(u\_0)$$
于是：
- 当$\symbfit{H}$是正定矩阵时，$\symbfit{u}\_0$是极小值点；
- 当$\symbfit{H}$是负定矩阵时，$\symbfit{u}\_0$是极大值点；
- 当$\symbfit{H}$不定时，$\symbfit{u}\_0$既不是极大值点也不是极小值点。

**使用主子式判断正负定**  设 $\symbfit{u}^{\mathrm{T}}\symbfit{A}\symbfit{u}$ 是二次型，$\symbfit{A}$是一个对称矩阵。对于$i = 1, 2, \cdots, n$，记 $d\_i$为矩阵$\symbfit{A}$的左上角$i \times i$子矩阵的行列式，二次型是正定的，当且仅当对于所有 $i$，$d\_i > 0$ 成立。二次型是负定的，当且仅当对于所有$i$，$(-1)^id\_i>0$成立。

## 非孤立稳定点

函数 $D: \mathbb{R}^n \to \mathbb{R}$的临界点$\symbfit{u}\_0$（即满足 $\nabla D(\symbfit{u}) = 0$的点）称为**退化**的，如果$D$的 Hessian 矩阵$\symbfit{H}$在点$\symbfit{u}\_0$处是奇异的（$\det \symbfit{H}=0$）。
**定理**  假设$D$的临界点$\symbfit{u}\_0$是非退化的，即它的 Hessian 矩阵是非退化的。那么一定存在$\symbfit{u}\_0$的一个邻域$U$，使得对于所有 $\symbfit{u} \in U - \{ \symbfit{u}\_0 \}$，$\nabla D(\symbfit{u}) \neq 0$成立。（具有上述性质的点$\symbfit{u}\_0$ 称为**孤立临界点**）
上述定理的逆定理通常不成立。所有的非退化临界点是孤立的，但是一些孤立临界点是退化的。

跟踪临界点曲线的一般方法如下：
1. 采用 IPP 算法进行低精度的包围盒搜索。例如，如果初始包围盒为$[0,1]^n$，求根算法的精度可以取为$10^{-2}$或$10^{-3}$。
2. 检查剩余的包围盒。如果存在几个包围盒相邻，那么解集中可能存在一条曲线。
3. 在第2步的包围盒中，用 Newton-Raphson 迭代方法进行高精度求根，并检查所得根的 Hessian 矩阵。如果它是奇异的，很有可能存在一条曲线。据此，从所有根中选择一个根作为跟踪法的起始点。

