---
title: 洛谷P3705 [SDOI2017]新生舞会 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:56:12
comments: true
tags: 
 - 分数规划
 - 网络流
 - 洛谷
keywords: 
description: 洛谷P3705
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/50.jpg
mathjax: true
---

# LINK

[洛谷P3705](https://www.luogu.com.cn/problem/P3705)

# 思路

又是道0/1分数规划。。。
还是那个套路，$C=\frac{a_1+a_2+...+a_n}{b_1+b_2+...+b_n}$转换成$\sum (a_i-C\times b_i)=0$
二分答案即可。条件判断使用最大费用最大流。（不会zkw 555

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 305
#define MAXM 30005
#define LD long double

int N;
int hd[MAXN], nxt[MAXM], to[MAXM], val[MAXM], tot(1);
LD c[MAXM];
int S, T;

inline void Add( Re int x, Re int y, Re int z, Re LD k ){
	nxt[++tot] = hd[x]; hd[x] = tot; to[tot] = y; val[tot] = z; c[tot] = k;
	nxt[++tot] = hd[y]; hd[y] = tot; to[tot] = x; val[tot] = 0; c[tot] = -k;
}

queue<int> Q;
LD ans, d[MAXN];
bool vis[MAXN];
int pre[MAXN];
int a[MAXN][MAXN], b[MAXN][MAXN];

inline bool SPFA(){
	for ( Re int i = S; i <= T; ++i ) d[i] = -1e11;
	while( !Q.empty() ) Q.pop();
	memset( vis, 0, sizeof vis );
	 
	d[S] = 0; Q.push(S); vis[S] = 1;
	while( !Q.empty() ){
		Re int t(Q.front()); Q.pop(); vis[t] = 0;
		for ( Re int i = hd[t]; i; i = nxt[i] ){
			if ( val[i] && d[t] + c[i] > d[to[i]] ){
				d[to[i]] = d[t] + c[i]; pre[to[i]] = i;
				if ( !vis[to[i]] ) vis[to[i]] = 1, Q.push(to[i]);
			}
		}
	}
	if ( d[T] <= -5e10 ) return 0;
	Re int fl(INT_MAX);
	for ( Re int i = T; i != S; i = to[pre[i] ^ 1] ) fl = min( fl, val[pre[i]] );
	for ( Re int i = T; i != S; i = to[pre[i] ^ 1] ) val[pre[i]] -= fl, val[pre[i] ^ 1] += fl;
	ans += (LD)fl * d[T];
	return 1;
}

inline bool check( Re LD x ){
	memset( hd, 0, sizeof hd ); tot = 1;
	for ( int i = 1; i <= N; ++i ) Add( S, i, 1, 0 );
	for ( int i = 1; i <= N; ++i ) Add( i + N, T, 1, 0 );
	for ( int i = 1; i <= N; ++i )
		for ( int j = 1; j <= N; ++j )
			Add( i, j + N, 1, (LD)a[i][j] - x * (LD)b[i][j] );
	ans = 0;
	while( SPFA() );
	return ans >= 0;
}

int main(){
	scanf( "%d", &N ); S = 0; T = N << 1 | 1;
	for ( int i = 1; i <= N; ++i )
		for ( int j = 1; j <= N; ++j )
			scanf( "%d", &a[i][j] );
	for ( int i = 1; i <= N; ++i )
		for ( int j = 1; j <= N; ++j )
			scanf( "%d", &b[i][j] );
	LD l(0), r(1e4), mid, ans(0);
	while ( l + 1e-7 < r ){
		mid = ( l + r ) / 2;
		if ( check( mid ) ) ans = mid, l = mid;
		else r = mid;
	}
	printf( "%.6Lf\n", ans );
	return 0;
}
```

