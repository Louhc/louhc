---
title: 洛谷P4322 [JSOI2016]最佳团体 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:59:19
comments: true
tags: 
 - 分数规划
 - 背包dp
 - 洛谷
keywords: 
description: 洛谷P4322
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/11.jpg
mathjax: true
---

# LINK

[洛谷P4322](https://www.luogu.com.cn/problem/P4322)

# 思路

十分裸的0/1分数规划。

$\frac{P_1+P_2+..P_k}{S_1+S_2+..S_k}= Ans$转换成$\sum (P_i-S_i\times Ans) = 0$

二分答案即可。条件判断可以使用树上分组背包DP解决。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 2505
#define LD double

int K, N; 
int S[MAXN], P[MAXN], R, sz[MAXN];
int hd[MAXN], nxt[MAXN << 1], to[MAXN << 1], tot;
LD f[MAXN][MAXN], v[MAXN];

inline void Add( Re int x, Re int y ){ nxt[++tot] = hd[x]; hd[x] = tot; to[tot] = y; }

void DFS( Re int x ){
	f[x][0] = 0;
	for ( Re int i = 1; i <= K; ++i ) f[x][i] = -1e10;
	sz[x] = 1;
	
	for ( Re int i = hd[x]; i; i = nxt[i] ){
		DFS(to[i]);
		sz[x] += sz[to[i]];
		
		
		int t(min( sz[x], K ));
		for ( Re int k = t; k >= 1; --k )
			for ( Re int j = 1; j <= sz[to[i]]; ++j )
				if ( k - j >= 0 ) f[x][k] = max( f[x][k], f[x][k - j] + f[to[i]][j] );
				else break;
	}
	for ( Re int i = min( K, sz[x] ); i >= 1; --i ) f[x][i] = f[x][i - 1] + v[x];
}

inline bool check( Re LD x ){
	for ( Re int i = 1; i <= N; ++i ) v[i] = (LD)P[i] - (LD)S[i] * x;
	DFS( 0 );
	return f[0][K] >= 0;
}

int main(){
	scanf( "%d%d", &K, &N ); K++;
	for ( Re int i = 1; i <= N; ++i )
		scanf( "%d%d%d", &S[i], &P[i], &R ), Add( R, i );
	
	Re LD l(0), r(10000), mid, ans(0);
	while ( r - l > 0.0001 ){
		mid = ( l + r ) / 2;
		if ( check( mid ) ) l = mid, ans = mid;
		else r = mid;
	}
	printf( "%.3lf\n", ans );
	return 0;
}
```