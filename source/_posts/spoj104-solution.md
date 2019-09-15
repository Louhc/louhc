---
title: spoj104 HIGH - Highways 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 07:58:08
comments: true
tags:
 - 高斯消元
 - 矩阵树定理
 - spoj
keywords:
description: spoj104
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/71.jpg
mathjax: true
---

# LINK

[spoj HIGH](https://www.spoj.com/problems/HIGH/)
[luogu remote judge SP104](https://www.luogu.org/problem/SP104)

# 前置芝士

高斯消元，矩阵相关。

# 思路

有一种神奇的定理——Matrix-Tree定理！
大概内容如下：

* 邻接矩阵$A$，若有一条边$(u,v)$，$A_{u,v}+1,A_{v,u}+1$.
* 度数矩阵$D$，$D_{i,i}=$点$i$的度数.
* 基尔霍夫矩阵$C=D-A$.
* 该图的生成树个数就是$C_{i,i}$的余子式（$1\le i \le N$都可以）下面我选择的是$C_{N,N}$，所以直接N--即可.
* 若该图不连通，$C_{i,i}$的余子式肯定是$0$.

具体证明去问度娘，~~因为我也不会~~。
然后只要用高斯消元求出行列式即可。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 20
#define LD long double

int T, N, M, x, y;
LD a[MAXN][MAXN];

int main(){
	scanf( "%d", &T );
	while( T-- ){
		scanf( "%d%d", &N, &M ); N--;
		memset( a, 0, sizeof a );
		for ( int i = 1; i <= M; ++i ) scanf( "%d%d", &x, &y ), a[x][x] += 1, a[y][y] += 1, a[x][y] -= 1, a[y][x] -= 1;
		
		bool flg(1);
		
		for ( int i = 1; i <= N; ++i ){
			int mx(i);
			for ( int j = i + 1; j <= N; ++j ) if ( a[mx][i] < a[j][i] ) mx = j;
			
			if ( i != mx ) for ( int j = i; j <= N; ++j ) swap( a[i][j], a[mx][j] );
			
			if ( a[i][i] < 1e-8 && a[i][i] > -1e-8 ){ printf( "0\n" ); flg = 0; break; }

			for ( int j = i + 1; j <= N; ++j ){
				LD t(a[j][i] / a[i][i]);
				for ( int k = i; k <= N; ++k ) a[j][k] -= a[i][k] * t;
			}
		}
		if ( flg ){
			LD ans(1);
			for ( int i = 1; i <= N; ++i ) ans *= a[i][i];
			printf( "%.0Lf\n", ans >= 0 ? ans : -ans );
		}
	}
	return 0;
}
```