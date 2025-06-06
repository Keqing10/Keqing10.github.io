---
title: 计算浮点数的末位精度ulp
category_bar: true
math: true
tags: [浮点数， 误差分析]
category: [数据结构与算法]
date: 2025-06-06 10:55:31
---

## 双精度浮点数的二进制表示
根据IEEE标准，`double`类型具有64位：一个符号位$s$，11位有偏指数$e=E+1023$和位串$b_1b_2\cdots b_{52}$组成的52位小数尾数$m$，并且总是隐藏首位$b_0=1$（这样$1\leqslant B=1.m\leqslant 2$）,这样浮点数为
$$
X=(-1)^s2^EB.
$$

根据上述标准，可表示数分为五类：
1. 若$e=2047$且$m\neq 0$，则$X=\mathrm{NaN}$（非数）；
2. 若$e=2047$且$m=0$，则$X=\pm\infty$，当$s=0$时为正，$s=1$时为负；
3. 若$0<e<2047$，则$X$是一个**规格数**（normalized number）：$X=(-1)^s2^{e-1023}1.m$；
4. 若$e=0$且$m\neq 0$，则$X$是一个**非规格数**（denormalized nubmer）：$X=(-1)^s2^{-1022}0.m$；
5. 若$e=0$且$m=0$，则$X=\pm0$，且运算时$-0\equiv +0$。

浮点数是全体实数的真子集，在原点左右对称但不均匀地分布。因此某个实数（满足`DBL_MIN < x < DBL_MAX`）并不一定能被浮点数精确表示，但可以知道它在某两个相邻浮点数之间。浮点计算具有误差，两个浮点数的运算结果不一定等于理论结果。在解方程等场景中，为了鲁棒性，往往需要扩大实数的表示区间来确保包含正确的解。

## 舍入区间运算
**舍入区间运算**（RIA，rounded interval arithmetic）可以保证区间计算所得端点总是可以包含精确计算结果，定义如下：
$$\begin{gather*}
[a,b]+[c,d]=[(a + c)-\varepsilon_{\mathrm{l}},(b + d)+\varepsilon_{\mathrm{u}}]\newline
[a,b]-[c,d]=[(a - d)-\varepsilon_{\mathrm{l}},(b - c)+\varepsilon_{\mathrm{u}}]\newline
[a,b]\cdot[c,d]=[\min(a\cdot c,a\cdot d,b\cdot c,b\cdot d)-\varepsilon_{\mathrm{l}},\max(a\cdot c,a\cdot d,b\cdot c,b\cdot d)+\varepsilon_{\mathrm{u}}]\newline
[a,b]/[c,d]=[\min(a/c,a/d,b/c,b/d)-\varepsilon_{\mathrm{l}},\max(a/c,a/d,b/c,b/d)+\varepsilon_{\mathrm{u}}]
\end{gather*}$$
其中$\varepsilon_{\mathrm{l}}$和$\varepsilon_{\mathrm{u}}$是末位精度（unit-in-last-place），记作$ulp_{\mathrm{l}}$和$ulp_{\mathrm{u}}$。当采用舍入区间运算执行标准区间数的运算时，**区间的下界减小到包含前一个相继的浮点数**，它比区间下界小$ulp_{\mathrm{l}}$，类似地，**区间上界增大$ulp_{\mathrm{u}}$以包含后一个相继的浮点数**。这样，区间宽度增大$ulp_{\mathrm{l}}+ulp_{\mathrm{u}}$，从而保证包含精确区间计算结果。


## 计算ulp
对于一个双精度浮点数$X=(-1)^s2^EB$，有效数字
$$B=1+b_12^{-1}+b_22^{-2}+\cdots+b_{52}2^{-52}$$
最小的有效位$b_{52}$代表$2^{-52}$，所以$ulp=2^{E-52}$。因此需要先提取浮点数的指数部分。

### 提取指数
从低位算起，提取double变量的第53到63位，共11位。有多种方法，如下所示：
```cpp
double x;
// 法1：直接操作地址，不安全
int64_t exp = (*(int64_t*)&x >> 52) & 0x7ff;
// 法2：借助联合体，较安全
union { double d; int64_t l; } u = {x};
int64_t exp = (u.l >> 52) & 0x7ff;
// 法3：C++20起支持，最安全
int64_t exp = (bit_cast<int64_t>(x) >> 52) & 0x7ff;
```

### 算法
#### C标准库
使用`<cmath>`库中的函数。
```cpp
double ulp(double x) {
    int exp;  // exp = E + 1
    frexp(x, &exp);
    return ldexp(0.5, exp - 52);  // ulp
}
// 或
double ulp(double x) {
    if (x == 0.0) return 0;
    return nextafter(x, INFINITY) - x;
}
```

#### 远古算法
> 《计算机辅助设计与制造中的外形分析》第4.8.2节

由于不支持长整型，使用`short`数组存储双精度浮点数的二进制表示。

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

注意：该算法对所有规格数输入能够正确处理，无论结果是否为规格数；而对非规格数和0全部返回0。

#### 现代算法
输入输出与上述算法保持一致，也可以调整对特殊情况的输出。
```cpp
double ulp(double x) {
	if (!isfinite(x)) return NAN; // 检查是否为有限值，处理 NaN/Inf
	if (x == 0.0) return 0; // 处理0，原书结果为0，非DBL_MIN

	int64_t bits = bit_cast<int64_t>(x);  // 逐位转为整型
	int64_t exp = (bits >> 52) & 0x7ff;  // 提取指数
	double ulp;
	if (exp > 52) {
		ulp = bit_cast<double>((exp - 52) << 52);
	}
	else if (exp == 0) {  // 补充：x是非规格数
		ulp = 0;  // 原书未处理，结果为0；非DBL_TRUE_MIN最小非规格数
	}
	else {
		ulp = bit_cast<double>(uint64_t(1) << (exp - 1));
	}
	return ulp;
}
```

#### 效率比较
测试环境为Windows 11，Visual Studio 2022。
使用$10^6$个随机浮点数测试，可以得到所有的方法近似对每个浮点数处理都在20 ns左右。

| 方法        | 耗时（秒） |
| ----------- | ---------- |
| 长整型      | 0.0172766  |
| frexp+ldexp | 0.0182978  |
| short数组   | 0.0216013  |
| nextafter   | 0.0229995  |

对于较大浮点数，各方法效率差异不明显，当输入浮点数非常非常小时（ulp是非规格数），差异会更加明显。固定输入`x=1.25e-295`，重复$10^6$次，耗时如下。

| 方法        | 耗时（秒）   |
| ----------- | ------------ |
| 长整型      | 0.0194614 秒 |
| nextafter   | 0.0197138 秒 |
| short数组   | 0.0226829 秒 |
| frexp+ldexp | 0.0536366 秒 |


## 舍入区间算法的实现
对于给定的$x$，目的是构造最紧的区间$[x_{\mathrm{l}},x_{\mathrm{u}}]$，使得$x_{\mathrm{l}}$为不大于$x$的最大可表示数，$x_{\mathrm{u}}$为不小于$x$的最小可表示数：
$$x_{\mathrm{l}}\leqslant x\leqslant x_{\mathrm{u}}$$
特别地，当$x$可以精确表示时，有$x_{\mathrm{l}}= x= x_{\mathrm{u}}$。

首先定义区间如下。
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
通过ulp进行计算，有$x_{\mathrm{l}}<x<x_{\mathrm{u}}$。
```cpp
Interval add(Interval a, Interval b) {
	double low = a.low + b.low;
	double upp = a.upp + b.upp;
	return Interval(low - ulp(low), upp + ulp(upp));
}
```
远古时代的SGI系统具有一个特殊函数`swapRM()`可以用来设定IEEE 754舍入模式。现在可以使用标准库`<cfenv>`中的`std::fesetround()`函数实现类似的功能。满足$x_{\mathrm{l}}\leqslant x\leqslant x_{\mathrm{u}}$。
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

