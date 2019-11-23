---
title: NOWCODER14962 Alice和Bob赌糖果 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-11-23 09:02:42
comments: true
tags:
 - 期望概率
 - 牛客网
keywords: 
description: NOWCODER14962
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/47.jpg
mathjax: true
---

# LINK

[NOWCODER14962](https://ac.nowcoder.com/acm/problem/14962)

# 思路

首先,我们可以忽略平局的情况.因为如果我们过滤掉平局的情况,最终结果仍然不变.

我们记Alice输的概率为 $p$ ,赢的概率为 $1-p$ , 当Alice手头有 $i$ 颗糖果时,最终获胜的概率是 $f_i$.

然后我们就可以列出转移方程:

$f_0=0,f_{n+m}=1$

$f_{i}=(1-p)f_{i-1}+pf_{i+1},0 < i < n+m$

很明显这玩意不能直接转移,如果 $n,m$ 比较小的时候我们可以高斯消元解方程,但是这题数据范围是$10^5$,用高斯消元会$TLE$.

于是我们手动解方程.(滑稽

把每一个$f_i$表示成$k_if_{i+1}$的形式.

$f_0=0f_1$,因此$k_0=0$.

$f_i=(1-p)f_{i-1}+pf_{i+1}=(1-p)k_{i-1}f_i+pf_{i+1}\Rightarrow (1-(1-p)k_{i-1})f_i=pf_{i+1}\Rightarrow f_i=\frac{p}{1-(1-p)k_{i-1}}f_{i+1}\Rightarrow k_i=\frac{p}{1-(1-p)k_{i-1}}$

于是我们就可以 $O(n)$ 推出所有的 $k$ 了.

因为 $f_{n+m}=1$,我们可以倒推出所有的 $f$.

最终答案就是 $f_n=\prod_{i=n}^{n+m-1}k_i$.注意特判 $n=0$ 的情况.

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define i64 long long
#define f80 long double
#define rgt register
#define fp( i, b, e ) for ( int i(b), I(e); i <= I; ++i )
#define fd( i, b, e ) for ( int i(b), I(e); i >= I; --i )
#define go( i, b ) for ( int i(b), v(to[i]); i; v = to[i = nxt[i]] )
template<typename T> inline bool cmax( T &x, T y ){ return x < y ? x = y, 1 : 0; }
template<typename T> inline bool cmin( T &x, T y ){ return y < x ? x = y, 1 : 0; }

int N, l, r, M, L, R;
double p, s, t;

signed main(){
	cin >> N >> l >> r >> M >> L >> R;
	if ( !N ) return printf( "0.00000\n" ), 0;
	fp( i, l, r ) fp( j, L, R )
		s += i != j ? 1 : 0, p += i > j ? 1 : 0;
	p /= s, s = 1, t = 0;
	fp( i, 1, N + M - 1 ){
		t = p / (1 - (1 - p) * t);
		if ( i >= N ) s *= t;
	} printf( "%.5lf\n", s );
	return 0;
}
```