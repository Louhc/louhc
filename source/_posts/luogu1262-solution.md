---
title: 洛谷P1262 间谍网络 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:46:23
comments: true
tags: 
 - tarjan
 - 洛谷
keywords: 
description: 洛谷P1262
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/18.jpg
mathjax: true
---

# LINK

[洛谷P1262](https://www.luogu.org/problem/P1262)

# 思路

如果A掌握B的信息，那么我们就建有向边A->B，构成一个图。
每个点的权值就是收买该间谍需要花的钱，如果不能收买，那就赋为$\infty$。
我们的任务就是取Sum(点权)最少的若干个点，以这几个点为起点，能遍历整张图。
当然，Sum(点权)不能为$\infty$。（注意下 $\infty + \infty = \infty \ \  ,\infty + x = \infty$）

因为在一个强连通分量中，任何一个点都能遍历到每个节点。所以，对于一个强连通分量，完全可以看成一个点，点权为该强连通分量包含的点的最小点权。

所以缩点即可。以下所说的点均为“缩”过的点。

入度为0的点**绝对**要取，而取来后**绝对**已经所有点都能访问到。

这个很好证明。入度为0的点绝对要取是不难理解的，因为它不可能被其他点访问。由于缩点后的图不可能构成环，所以入度不为0的点肯定可以被已经访问过的点访问到。

注意下不能被控制的(编号最小)点**不一定**入度为0。

如这组数据:

### Input Data

```
4
1
4 100
4
2 4
2 1
4 3
1 3

```

### Output Data

```
NO
1
```



# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 3005
#define MAXM 8005

int n, p, r;
int hd[MAXN], nxt[MAXM], to[MAXM], tot(1);
int dfn[MAXN], low[MAXN], num, stk[MAXN], tp, c[MAXN], cnt;
int pr[MAXN], f[MAXN];
int x, y;
bool vs[MAXN], v[MAXN];

void Add( int x, int y){ nxt[++tot] = hd[x]; hd[x] = tot; to[tot] = y; }

void Tarjan( int x ){
	dfn[x] = low[x] = ++tot; stk[++tp] = x;
	for ( int i = hd[x]; i; i = nxt[i] ){
		if ( !dfn[to[i]] ) Tarjan( to[i] ), low[x] = min( low[x], low[to[i]] );
		else if ( !c[to[i]] ) low[x] = min( low[x], dfn[to[i]] );
	}
	if ( low[x] == dfn[x] ){
		c[x] = ++cnt; f[cnt] = pr[x];
		while( stk[tp] != x ) f[cnt] = min( f[cnt], pr[stk[tp]] ), c[stk[tp--]] = cnt;
		tp--;
	}
}

//记忆化搜索
//Memory search 
void DFS( int x ){
	v[x] = 1;
	for ( int i = hd[x]; i; i = nxt[i] )
		if ( !v[to[i]] ) DFS( to[i] );
}

int main(){
	scanf( "%d%d", &n, &p );
	for ( int i = 1; i <= n; ++i ) pr[i] = INT_MAX;
	for ( int i = 1; i <= p; ++i ) scanf( "%d%d", &x, &y ), pr[x] = y;
	scanf( "%d", &r );
	for ( int i = 1; i <= r; ++i ) scanf( "%d%d", &x, &y ), Add( x, y );
	
	// 先判断是否能控制全部间谍,即把所有能收买的间谍都收买
	// To see whether all spies can be under control. 
	for ( int i = 1; i <= n; ++i ) if ( pr[i] < INT_MAX && !v[i] ) DFS( i );
	for ( int i = 1; i <= n; ++i ) if ( !v[i] ){ printf( "NO\n%d\n", i ); return 0; }
	//------------------判断结束 Ended--------------------
	//因为已经确定所有间谍都能被控制,那就大胆地取来所有入度为0的节点(当然,是缩点后的图)
	// Because all spies can be controled, get all ponits which has no indegree(After Tarjan, certainly).
	for ( int i = 1; i <= n; ++i ) if ( !c[i] ) Tarjan( i );
	//当然, 并不需要建边，可以直接判断入度是否为0
	//Certainly, it is unnecessary to connect edges.You can DIRECTLY know whether a point has no indegree.
	for ( int i = 1; i <= n; ++i )
		for ( int j = hd[i]; j; j = nxt[j] )
			if ( c[i] != c[to[j]] ) vs[c[to[j]]] = 1;
	int ans(0); 
	for ( int i = 1; i <= cnt; ++i ) if ( !vs[i] ) ans += f[i];
	printf( "YES\n%d\n", ans );
	return 0;
}
```

注：为了防止中文乱码情况，代码注释给出英文版。