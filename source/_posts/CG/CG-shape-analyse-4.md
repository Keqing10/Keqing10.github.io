---
title: 第4章 非线性多项式求解和鲁棒性问题
category_bar: true
math: true
tags: [浮点数, 误差分析, 区间运算]
category: [CG, 《计算机辅助设计与制造中的外形分析》读书笔记]
date: 2025-06-06 21:07:48
---

# 引言
**控制方程**（Governing Equations）通常可以归结为线性方程组的求解：
$$
\symbfit{f}(\symbfit{X})=0
$$
其中$\symbfit{f}$包含$n$个函数$f_1,f_2,\cdots,f_n$，每个函数是一个包含$l$个独立变量$x_1,x_2,\cdots,x_l$的多项式。
# 局部求解方法
单变量的牛顿法：求解方程$f(x)=0$，把根的初始值记为$x_0$，方程在初始值附近的线性逼近为：$f(x)\approx f(x_0)+(x-x_0)f'(x_0)$，如果$f'(x_i)\neq 0$，可以得到如下迭代公式：
$$
x_{i + 1} = x_i - \frac{f(x_i)}{f'(x_i)}, \quad i = 0, 1, 2 \cdots$$
为了防止迭代发散，一种改进的单变量牛顿法为：
$$x_{i + 1} = x_i - \mu \cdot \frac{f(x_i)}{f'(x_i)}$$
其中：$\mu = \max\lbrace 1, \frac{1}{2}, \frac{1}{4}, \cdots\rbrace$  使得 $|f(x_{i + 1})| < |f(x_i)|$成立。
如果$n=l$，即方程个数等于未知数个数，多变量牛顿法如下：
$$
\symbfit{x}_{i+1}=\symbfit{x}_i+\Delta\symbfit{x}_i
$$
其中$\Delta\symbfit{x}_i$满足$\symbfit{J}(\symbfit{x}_i)\cdot\Delta\symbfit{x}_i=-\symbfit{f}(\symbfit{x}_i)$，$\symbfit{J}(\symbfit{x}_i)=\left[ \frac{\partial f_i}{\partial x_k} \right]$是Jacobian矩阵。
# 整体求解方法的分类
## 代数与混合方法
主要基于消元理论。
- 结式方法
- Gröbner基方法

## 同伦（homotopy）（连续）方法
## 剖分方法
实际中最常用，通常是有效、稳定的。可以和区间方法相结合。但不具有代数方法的一般性，只能够隔离零维的根。

# 投影多面体算法
 **投影多面体（Projected Polyhedron，PP）算法**，是关于$n$维非线性多项式方程组的一种迭代整体求根算法，属于剖分求根方法。PP算法计算$m$次多项式$f(x)=c_0+c_1x+c_2x^2+\cdots+c_mx^m=0$在区间$a\leqslant x\leqslant b$上全部根的步骤如下：
 1. 进行仿射变换$x=a+t(b-a)$使得变量满足$t\in [0,1]$，此时多项式变为：$f(t)=\sum_{i=0}^mc_i^Mt^i$。（此时多项式不应使用$f$表示，这里与原文保持一致。）
 2. 将单项式基转化为Bernstein基：
$$
f(t) = \sum_{i = 0}^{m} c_{i}^{B} B_{i,m}(t)
$$
其中$$c_i^B = \sum_{j = 0}^i \frac{\binom{i}{j}}{\binom{m}{j}} c_j^M.$$
3. 基于Bernstein多项式的线性精度性质，生成函数$f(t)$的图，此时是一条Bézier曲线：
$$\boldsymbol{f}(t) = \begin{bmatrix}
t \newline f(t)
\end{bmatrix} = \sum_{i = 0}^{m} \binom{\frac{i}{m}}{c_{i}^{B}} B_{i, m}(t)
$$
其中$[\frac{i}{m}\quad c_i^B]^{\mathrm{T}}$为控制顶点。这样单变量多项式求根的数值问题转化为Bézier曲线与参数轴求交的几何问题。
4. 构造Bézier曲线的凸包。
5. 计算凸包与参数轴的交点。
6. 通过de Casteljau剖分算法，抛弃不含有根的区域，求得$[0,1]$中包含根的一个子区域。
7. 如果子区域充分小，可以推断其中含有一个根并返回；如果子区域含有不止一个根，那么该子区域将不会进一步缩小，此时利用de Casteljau算法将子区域二分，对于每一段曲线，返回第4步。

将问题归结于$n$个未知量、$n$个非线性多项式方程式组成的方程组，成为**平衡方程组**；如果是$l$个未知量、$n$个非线性多项式方程，$n>l$成为过约束（overconstrained）方程组，$n<l$成为欠约束（underconstrained）方程组。

## 一种$n$维投影多项式算法
假设给定$n$个非线性多项式$f_1,f_2,\cdots,f_n$，每个多项式具有$l$个独立变量$x_1,x_2,\cdots,x_l$，记$m_i^{(k)}$为多项式$f_k$的变量$x_i$的次数，则$\symbfit{M}^{(k)}=(m_1^{(k)},m_2^{(k)},\cdots,m_l^{(k)})$描述了多项式$f_k$的次数信息；给定$\mathbb{R}^l$中的一个$l$维矩形子区域：
$$
B=[a_1,b_1]\times[a_2,b_2]\times\cdots\times[a_l,b_l]
$$
希望求得所有点$\symbfit{x}\in B$满足：
$$f_1(\boldsymbol{x}) = f_2(\boldsymbol{x}) = \cdots = f_n(\boldsymbol{x}) = 0$$
通过对所有变量进行仿射参数变换$x_i=a_i+u_i(b_i-a_i)$可以将问题简化为计算所有点$\symbfit{u}\in[0,1]^l$满足：
$$f_1(\boldsymbol{u}) = f_2(\boldsymbol{u}) = \cdots = f_n(\boldsymbol{u}) = 0$$
后续依然使用Bernstein多项式表示，将代数问题转化为几何问题解决。

# 具有平方根的非线性多项式方程组的辅助变量方法
有如下方程：
$$f(\symbfit{x}) + g(\symbfit{x})\sqrt{h(\symbfit{x})} = 0$$
其中$\symbfit{x}$是有$l$维未知向量，$f,g,h$是定义在$[0,1]^l$上的多变量多项式。使用平方法得到$f^2-g^2h=0$会导致更高阶的方程和一些不必要的根。使用辅助变量法如下。引入辅助变量$\tau\in[a,b]$满足$\tau^2=h(\symbfit{x})$ ，定义域满足：
$$\begin{gather}
a = \sqrt{\max\lbrace0,\min h\rbrace}\newline
b = \sqrt{\max h}
\end{gather}$$
再进行放缩$\sigma\equiv\frac{\tau-a}{\tau-b}$，使得$\sigma\in[0,1]$。于是得到如下具有$l+1$个变量、两个非线性多项式方程的方程组
$$
\begin{cases}
f+g[a+\sigma(b-a)]=0 \newline
[a+\sigma(b-a)]^2-h=0
\end{cases}
$$
可以使用PP算法求解。

# 鲁棒性问题
**浮点运算**（FPA，floating point arithmetic），容易产生误差。浮点数在数轴上并不均匀分布。基于浮点运算的非线性求解更快但通常不鲁棒。
**有理运算**（RA，rational arithmetic）是有理数之间的非近似计算，基于有理运算的非线性多项式求解是鲁棒的，但通常会占用大量内存且耗时。
**区间运算**（IA，interval arithmetic）有效地解决了以上问题，与有理运算相比时间和内存耗费较少，并且可以鲁棒地删除不含有根的区域。
# 区间运算
区间定义为如下实数集合：$[a,b]=\lbrace x|a\leqslant x\leqslant b\rbrace$。
区间可以定义相等、交、并运算。
区间的顺序定义为：
$$
[a,b]<[c,d]\text{当且仅当}b<c
$$
区间的$[a,b]$的宽度为$b-a$，绝对值为$|[a,b]|=\max\lbrace|a|,|b|\rbrace$。
区间的算术运算定义为：
$$[a, b] \circ [c, d] = \lbrace x \circ y \mid x \in [a, b] \text{ 且 } y \in [c, d] \rbrace$$
其中$\circ$表示运算符$\circ\in\lbrace+,-,\cdot,/\rbrace$。
$$\begin{gather*}
[a,b]+[c,d]=[a + c,b + d]\newline
[a,b]-[c,d]=[a - d,b - c]\newline
[a,b]\cdot[c,d]=[\min(ac,ad,bc,bd),\max(ac,ad,bc,bd)]\newline
[a,b]/[c,d]=[\min(a/c,a/d,b/c,b/d),\max(a/c,a/d,b/c,b/d)]
\end{gather*}$$
其中要求除法运算中$0\notin [c,d]$。
## 区间算术运算的代数性质
区间运算具有**交换性**（commutative）、**结合性**（associative）和**子分配性**（subdistributive），但不具有分配性（distributive）。
$$\begin{gather*}
[a,b]+[c,d]=[c,d]+[a,b]\newline
[a,b]\cdot[c,d]=[c,d]\cdot[a,b]\newline
[a,b]+([c,d]+[e,f])=([a,b]+[c,d])+[e,f]\newline
[a,b]\cdot([c,d]\cdot[e,f])=([a,b]\cdot[c,d])\cdot[e,f]\newline
[a,b]\cdot([c,d]+[e,f])\subseteq[a,b]\cdot[c,d]+[a,b]\cdot[e,f]
\end{gather*}$$
# 舍入区间运算及其实现
**舍入区间运算**（RIA，rounded interval arithmetic）可以保证区间计算所得端点总是可以包含精确计算结果，定义如下：
$$\begin{gather*}
[a,b]+[c,d]=[(a + c)-\varepsilon_{\mathrm{l}},(b + d)+\varepsilon_{\mathrm{u}}]\newline
[a,b]-[c,d]=[(a - d)-\varepsilon_{\mathrm{l}},(b - c)+\varepsilon_{\mathrm{u}}]\newline
[a,b]\cdot[c,d]=[\min(a\cdot c,a\cdot d,b\cdot c,b\cdot d)-\varepsilon_{\mathrm{l}},\max(a\cdot c,a\cdot d,b\cdot c,b\cdot d)+\varepsilon_{\mathrm{u}}]\newline
[a,b]/[c,d]=[\min(a/c,a/d,b/c,b/d)-\varepsilon_{\mathrm{l}},\max(a/c,a/d,b/c,b/d)+\varepsilon_{\mathrm{u}}]
\end{gather*}$$
其中$\varepsilon_{\mathrm{l}}$和$\varepsilon_{\mathrm{u}}$是末位精度（unit-in-last-place），记作$ulp_{\mathrm{l}}$和$ulp_{\mathrm{u}}$。当采用舍入区间运算执行标准区间数的运算时，区间的下界减小到包含前一个相继的浮点数，它比区间下界小$ulp_{\mathrm{l}}$，类似地，区间上界增大$ulp_{\mathrm{u}}$以包含后一个相继的浮点数。这样，区间宽度增大$ulp_{\mathrm{l}}+ulp_{\mathrm{u}}$，从而保证包含精确区间计算结果。
## 双精度浮点运算
浮点数使用如下二进制表示：一个符号位$s$，一个整数指数$E$，和$p$位有效数字$B$，即$X=(-1)^s2^EB$，$B=b_0.b_1b_2\cdots b_{p-1}=\sum_{i=0}^{p-1}b_i2^{-i}$。
对于双精度运算，标准定义：$p=53$，$E_{\min}=-1022$和$E_{\max}=1023$。64位浮点数具有一个符号位$s$，11位有偏指数$e=E+1023$和位串$b_1b_2\cdots b_{52}$组成的52位小数尾数$m$，并且总是隐藏首位$b_0=1$（这样$1\leqslant B\leqslant 2$）。
根据上述标准，可表示数分为五类：
1. 若$e=2047$且$m\neq 0$，则$X=\mathrm{NaN}$（非数）；
2. 若$e=2047$且$m=0$，则$X=\pm\infty$，当$s=0$时为正，$s=1$时为负；
3. 若$0<e<2047$，则$X$是一个**规格数**（normalized number）：$X=(-1)^s2^{e-1023}1.m$；
4. 若$e=0$且$m\neq 0$，则$X$是一个**非规格数**（denormalized nubmer）：$X=(-1)^s2^{-1022}0.m$；
5. 若$e=0$且$m=0$，则$X=\pm0$，且运算时$-0\equiv +0$。

两个规格数的运算结果不总是能表示为规格数。采用非规格数，可以保证对所有规格数之间的运算，和满足$\left|x-y\right|\geqslant 4.9406564584124654\times10^{-324}$（最小非规格数）的非规格数，总是有$x=y\,\Leftrightarrow \,x-y=0$。
## 在二进制表示中提取指数部分
为了计算$ulp$，需要从数字的二进制表示中提取指数部分。对于一个双精度浮点数$X=(-1)^s2^EB$，有效数字
$$B=1+b_12^{-1}+b_22^{-2}+\cdots+b_{52}2^{-52}$$
最小的有效位$b_{52}$代表$2^{-52}$，所以$ulp=2^{E-52}$。
### 使用C标准库函数
使用C标准库`<cmath>`中的两个数学函数（浮点型参数/返回值也可以是float或long double）：
- `double frexp(double num, int* exp)`：提取指数。如果 `num` 为零，则返回零，并将零存储在 `*exp` 中。如果 `num` 不为零，如果没有错误发生，则返回范围 $(-1, -0.5]$或$[0.5, 1)$ 中的值 ，并将一个指数存储在 `*exp` 中，使得 $x\times2^{*\mathrm{exp}}= \mathrm{num}$。
- `double ldexp(double num, int exp)`：返回$\mathrm{num}\times2^{\mathrm{exp}}$。

```cpp
#include <cmath>
double ulp(double x) {
    int exp;  // exp = E + 1
    frexp(x, &exp);
    return ldexp(0.5, exp - 52);  // ulp
}
```

> 注意: 函数 $\mathrm{frexp}()$中假设$0.5\leq B < 1$, 而在 IEEE 754 中定义的非偏指数 $E$假设$1\leq B < 2$, 因此 $\mathrm{exp} = E + 1$。

由于调用标准库函数，算法运行较慢。
### 使用位运算
将二进制双精度浮点数直接当作64位整数操作，通过位运算可以方便地得到指数部分。
> 但是，大部分商业处理器和编程语言不支持 64 位整数，通常只支持 8、16 和 32 位整数运算。为了克服这个问题，我们可以用四个 16 位（短）整数数组来覆盖 64 位双精度数所占的存储位置。（出版于2001年）

旧算法：
```cpp
#define MSW 3 // 在MSVC中double是小端存储方式；若为大端存储，值为0
const uint16_t mask[16] = {
	0x0001, 0x0002, 0x0004, 0x0008,
	0x0010, 0x0020, 0x0040, 0x0080,
	0x0100, 0x0200, 0x0400, 0x0800,
	0x1000, 0x2000, 0x4000, 0x8000 };

double ulp(double x) {
	union Double { double dp; uint16_t sh[4]; } U, X;  // ulp, x
	X.dp = x;
	X.sh[MSW] &= 0x7ff0;  // 提取指数
	U.dp = 0.0;
	if (X.sh[MSW] > 0x0340) {  // ulp是规格数
		U.sh[MSW] = X.sh[MSW] - 0x0340;  // exp - 52
	}  // 0x0340 = 832 = 52 << 4
	else {  // ulp是非规格数
		int bit, el, word;
		el = (X.sh[MSW] >> 4) - 1;  // exp - 1
		word = el >> 4;
		if (MSW == 0) word = 3 - word;
		bit = el % 16;
		U.sh[word] |= mask[bit];
	}
	return U.dp;
}
```

C++20起支持`std::bit_cast<typename>`以进行安全的逐位类型转换。
```cpp
double ulp(double x) {
    if (!isfinite(x)) return NAN; // 检查是否为有限值，处理 NaN/Inf
    if (x == 0.0) return 0; // 处理0，原书结果为0，非DBL_MIN

    int64_t bits = bit_cast<int64_t>(x);  // 逐位转为整型
    int64_t exp = (bits >> 52) & 0x7ff;  // 提取指数
    double ulp;
    if (exp > 52) {
        ulp = bit_cast<double>((exp - 52) << 52);
    } else if (exp == 0) {  // 补充：x是非规格数
        ulp = 0;  // 原书未处理，结果为0；非DBL_TRUE_MIN最小非规格数
    } else {
        ulp = bit_cast<double>(uint64_t(1) << (exp - 1));
    }
    return ulp;
}
```

## 两种计算最终精度单位方法的比较
库函数算法随着ulp减小而增加，当输入浮点数小到其ulp是非规格数时，会显著慢于位运算方法。
## 硬件实现的舍入区间运算
对于给定的$x$，构造最紧的区间$[x_{\mathrm{l}},x_{\mathrm{u}}]$，使得$x_{\mathrm{l}}$为不大于$x$的最大可表示数，$x_{\mathrm{u}}$为不小于$x$的最小可表示数：
$$x_{\mathrm{l}}\leqslant x\leqslant x_{\mathrm{u}}$$
特别地，当$x$可以精确表示时，有$x_{\mathrm{l}}= x= x_{\mathrm{u}}$。
## 舍入区间算法的实现
首先定义区间。
```cpp
class Interval {
private:
    double low, upp;
public:
	Interval() : low(0), upp(0) {}
	Interval(double l, double u) : low(l), upp(u) {}
    friend Interval add(Interval, Interval);
	Interval operator+(Interval other) {
		return add(*this, other);
	}
};
```
### 软件实现
```cpp
Interval add(Interval a, Interval b) {
	double low = a.low + b.low;
	double upp = a.upp + b.upp;
	return Interval(low - ulp(low), upp + ulp(upp));
}
```
### 硬件实现
SGI系统具有一个特殊函数`swapRM()`可以用来设定IEEE 754舍入模式（现在可能已过时，可以使用标准库`<cfenv>`中的`std::fesetround`函数）。
```cpp
Interval add(Interval a, Interval b) {
    Interval c;
    swapRM(ROUND_TO_MINUS_INFINITY);
    c.low = a.low + b.low;
    swapRM(ROUND_TO_PLUS_INFINITY);
    c.upp = a.upp + b.upp;
    return c;
}
```

对于软件实现，$x_{\mathrm{l}}<x<x_{\mathrm{u}}$；对于硬件实现，$x_{\mathrm{l}}\leqslant x\leqslant x_{\mathrm{u}}$。

# 区间投影多面体算法
将PP算法推广至舍入区间运算环境下进行，即**区间投影多面体算法**（IPP，interval projected polyhedron algorithm）。
## 控制多项式方程的公式化（formulation）
- 如果给的那个Bézier曲线或曲面的控制顶点是浮点数，建议采用有理运算（RA）或舍入区间运算（RIA）。
- 如果给定曲线或曲面的控制顶点是非有理数，建议采用RIA以避免浮点运算的数值误差扩散。
- 如果在公式转化中应用了旋转运算，建议将Bernstein形式的非线性方程系数转换为以浮点数为边界的区间。

