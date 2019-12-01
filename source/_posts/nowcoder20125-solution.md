---
title: NOWCODER20125 [JLOI2010]足彩投注 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-12-01 15:22:38
comments: true
tags:
 - 期望概率
 - 牛客网
keywords: 
description: NOWCODER20125
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/34.jpg
mathjax: true
---

# LINK

[NOWCODER20125](https://ac.nowcoder.com/acm/problem/20125)
[洛谷P3849](https://www.luogu.com.cn/problem/P3849)

# 思路

题目又臭又长,其实没有什么卵用....  
真正有用的只有那个式子.根据乘法原理,我们可以转换成这样:  
$$ans=\frac MN\max\{\prod_{i=1}^n(a_{i,0}\times \frac{p_{i,0}}{q_{i,0}}+a_{i,1}\times \frac{p_{i,1}}{q_{i,1}}+a_{i,2}\times \frac{p_{i,2}}{q_{i,2}})\},a\in\{0,1\},\prod_{i=1}^n(a_{i,0}+a_{i,1}+a_{i,2})\le U$$  
于是设计状态 $f[i][j][k]$ 表示当前进行到 $i$ 场,有 $j$ 场 $(a_{i,0}+a_{i,1}+a_{i,2})$ 为 $2$,有 $k$ 场 $(a_{i,0}+a_{i,1}+a_{i,2})$ 为 $3$.  
因为 $j,k$ 都是$\log$ 级别的,所以总复杂度为 $O(n\log^2 U)$.  
最终结果可能会很大,最好不要用 `double`,用 `long double`,或者利用 $\log(xy)=\log x + \log y$ 来求.  
因为最终结果也要求取 $\log$,于是用后一种就OK了.  

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

const int _ = 10015;
int n, N, M, U;
double p[_][3], f[_][14][9];

signed main(){
	scanf( "%d%d%d%d", &n, &N, &M, &U );
	fp( i, 1, n ){
		double t1, t2, t3;
		scanf( "%lf%lf%lf%lf%lf%lf", p[i], p[i] + 1, p[i] + 2, &t1, &t2, &t3 );
		p[i][0] /= t1, p[i][1] /= t2, p[i][2] /= t3, sort(p[i], p[i] + 3),
		p[i][1] += p[i][2], p[i][0] += p[i][1]; //贪心选最大的几个加起来
		p[i][0] = log(p[i][0]), p[i][1] = log(p[i][1]), p[i][2] = log(p[i][2]);
	} double ans(0);
	fp( i, 1, n ){
		for ( int j(0), tj(1); tj <= U; ++j, tj *= 2 )
			for ( int k(0), tk(tj); tk <= U; ++k, tk *= 3 ){
				cmax( f[i][j][k], f[i - 1][j][k] + p[i][2] );
				if ( j ) cmax( f[i][j][k], f[i - 1][j - 1][k] + p[i][1] );
				if ( k ) cmax( f[i][j][k], f[i - 1][j][k - 1] + p[i][0] );
			}
	} fp( i, 0, 13 ) fp( j, 0, 8 ) cmax( ans, f[n][i][j] );
	printf( "%.3lf\n", ans + log(M) - log(N) );
	return 0;
}
```