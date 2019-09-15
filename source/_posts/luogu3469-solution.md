---
title: 洛谷P3469 [POI2008]BLO-Blockade 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:55:28
comments: true
tags: 
 - tarjan
 - 洛谷
keywords: 
description: 洛谷P3469
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/48.jpg
mathjax: true
---

# LINK

[洛谷P3469](https://www.luogu.org/problem/P3469)

# 思路

> I.设在搜索树T中以x为根的树包含的点集为SubTree(x)。
>
> II.这里去除割点可以理解为删除与该点相连的所有边。
>
> III.这里提到的连通块是指当某一割点去除时：1.其中任何两个点都能相互到达 2.没有更大的连通块包含该块 当然，根据II，我们把单独的X也看做一个连通块
>
> IV.为了方便，我们直接将(x,y)(满足x不能到y)成为“点对”
>
> V. \~S：S的补集，A^B：在A中不包括点B的所有点构成的集合

利用Tarjan查找割点的同时，我们可以找出该割点X去除后剩余的连通块（有两种情况，一种是在SubTree(X)^X中，另一种是\~SubTree(X)）。

只要能够理解割点的求解过程，这还是很好理解的，这里不再赘述。

然后要求“点对”。

对于一个点X，不管是否为割点，点对(i,j)为“点对”，当且仅当

> i != j且i、j属于两个不同的连通块

根据定义，很容易证明这个推论。

代码有多种写法，这里选取**我能想到的**最简单的写法。

在枚举连通块时，ans加上`s[to[i]] * ( n - s[to[i]] )`，即一次性处理一个连通块的所有点，它们与其他不属于这个连通块的点都构成“点对”。当然，别忘了X与\~SubTree(X)。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define LL long long
#define MAXN 100005
#define MAXM 1000005

int n, m;
int hd[MAXN], nxt[MAXM], to[MAXM], tot;
int dfn[MAXN], low[MAXN], root, num;
LL ans[MAXN];
int s[MAXN];

void Add( int x, int y ){ nxt[++tot] = hd[x]; hd[x] = tot; to[tot] = y; }

void DFS( int x ){
	s[x] = 1;
	low[x] = dfn[x] = ++num;
	LL b(0);
	for ( int i = hd[x]; i; i = nxt[i] ){
		if ( !dfn[to[i]] ){
			DFS( to[i] ); s[x] += s[to[i]];
			low[x] = min( low[x], low[to[i]] );
			if ( dfn[x] <= low[to[i]] ) ans[x] += (long long)s[to[i]] * ( n - s[to[i]] ), b += s[to[i]];//发现新的连通块！
		} else low[x] = min( low[x], dfn[to[i]] );
	}
	ans[x] += (long long)( n - b - 1 ) * ( b + 1 ) + ( n - 1 );//算上~SubTree(X)与X
}

int main(){
	scanf( "%d%d", &n, &m );
	for ( int i = 1; i <= m; ++i ){
		int x, y; scanf( "%d%d", &x, &y ); Add( x, y ); Add( y, x );
	}
	DFS( 1 );
	for ( int i = 1; i <= n; ++i ) printf( "%lld\n", ans[i] );
	return 0;
}

```