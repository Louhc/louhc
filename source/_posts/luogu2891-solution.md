---
title: 洛谷P2891 [USACO07OPEN]吃饭Dining 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:53:30
comments: true
tags: 
 - 网络流
keywords: 
description: 洛谷P2891
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/46.jpg
mathjax: true
---

# LINK

[洛谷P2891](https://www.luogu.com.cn/problem/P2891)  

# 思路

> 注意，以下出现的所有边边权皆为1，且其反向边边权为0

我们以牛、食物、饮料为点建图。
像这样：

![](https://img2018.cnblogs.com/blog/1431616/201812/1431616-20181214131744366-1260908236.png)

S(=0)表示额外建的一个起始点，Ri(=i+N+N)表示第i个菜，Di(=i+N+N+F)表示第i种饮料，由于牛只有一头，而网络流处理只经过一个点不方便，我们采用一种神奇方法——拆点！也就是说，把一头牛看做两个点，要匹配这头牛必须经过这头牛两点之间的边，这样就可以控制这头牛只匹配一次。如图，Pi(=i)、Pi'(=i+N)表示第i头牛。

然后建边。如图，将S与所有Ri相连，将所有的Di与T相连，S作为源点，T作为汇点。如果Pi喜欢Rj，就将Pi与Rj相连。如果Pi喜欢Dj，就将Dj与Pi'之间相连。当然，Pi与Pi'之间也要连一条边。

然后就可以套网络最大流辣。最后得出的答案即为满足的牛数。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define open(s) freopen( s".in", "r", stdin ), freopen( s".out", "w", stdout )
#define MAXN 405
#define MAXM 40005

int N, F, D;
int hd[MAXN], nxt[MAXM << 1], to[MAXM << 1], val[MAXM << 1], tot(1);
int pre[MAXN], e[MAXN], ans, dis[MAXN];
queue<int> Q;

int x, y;
int S, T;

void Add( int x, int y, int z ){ nxt[++tot] = hd[x]; hd[x] = tot; to[tot] = y; val[tot] = z; }

bool BFS(){
	while( !Q.empty() ) Q.pop();
	memset( dis, 0, sizeof dis );
	Q.push(S); dis[S] = 1;
	while( !Q.empty() ){
		x = Q.front(); Q.pop();
		for ( int i = hd[x]; i; i = nxt[i] )
			if ( val[i] && !dis[to[i]] ){
				dis[to[i]] = dis[x] + 1;
				Q.push( to[i] );
				if ( to[i] == T ) return 1;
			}
	}
	return 0;
}

int DFS( int x, int fl ){
	if ( x == T ) return fl;
	int res(fl), k;
	for ( int i = hd[x]; i && res; i = nxt[i] ){
		if ( val[i] && dis[to[i]] == dis[x] + 1 ){
			k = DFS( to[i], min( res, val[i] ) );
			if ( !k ) dis[to[i]] = 0;
			val[i] -= k; val[i^1] += k; res -= k;
		}
	}
	return fl - res;
}

int main(){
	scanf( "%d%d%d", &N, &F, &D );
	S = 0; T = N + N + F + D + 1;
	for ( int i = 1; i <= N; ++i ) Add( i, i + N, 1 ), Add( i + N, i, 0 );
	for ( int i = 1; i <= F; ++i ) Add( S, i + N + N, 1 ), Add( i + N + N, S, 0 );
	for ( int i = 1; i <= D; ++i ) Add( i + N + N + F, T, 1 ), Add( T, i + N + N + F, 0 );
	for ( int i = 1; i <= N; ++i ){
		int f, d, x; scanf( "%d%d", &f, &d );
		for ( int j = 1; j <= f; ++j ) scanf( "%d", &x ), Add( x + N + N, i, 1 ), Add( i, x + N + N, 0 );
		for ( int j = 1; j <= d; ++j ) scanf( "%d", &x ), Add( i + N, x + N + N + F, 1 ), Add( x + N + N + F, i + N, 0 );
	}
	int t;
	while( BFS() )
		while( ( t = DFS( S, 0x7f7f7f7f ) ) > 0 ) ans += t;
	printf( "%d\n", ans );
	return 0;
}


```