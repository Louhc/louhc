---
title: 洛谷P1231 教辅的组成 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:44:24
comments: true
tags: 
 - 网络流
 - 洛谷
keywords: 
description: 洛谷P1231
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/77.jpg
mathjax: true
---

# LINK

[洛谷P1231](https://www.luogu.com.cn/problem/P1231)

# 思路

> 注意，以下出现的所有边边权皆为1，且其反向边边权为0

我们以书、练习册、答案为点建图。
像这样：

![](https://img2018.cnblogs.com/blog/1431616/201812/1431616-20181214131744366-1260908236.png)

S(=0)表示额外建的一个起始点，Ri(=i+N1+N1)表示第i本练习册，Di(=i+N1+N1+N2)表示第i本答案，由于书只有一本，而网络流处理只经过一个点不方便，我们采用一种神奇方法——拆点！也就是说，把一本书看做两个点，要匹配这本书必须经过这本书两点之间的边，这样就可以控制这本书只匹配一次。如图，Pi(=i)、Pi'(=i+N1)表示第i本书。

然后建边。如图，将S与所有Ri相连，将所有的Di与T相连，S作为源点，T作为汇点。如果Pi能匹配Rj，就将Pi与Rj相连。如果Pi能匹配Dj，就将Dj与Pi'之间相连。当然，Pi与Pi'之间也要连一条边。

然后就可以套网络最大流辣。最后得出的答案即为最多的数目。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define open(s) freopen( s".in", "r", stdin ), freopen( s".out", "w", stdout )
#define MAXN 40005
#define MAXM 150000

int N1, N2, N3;
int M1, M2;
int hd[MAXN], nxt[MAXM << 1], to[MAXM << 1], val[MAXM << 1], tot(1);
int ans, dis[MAXN];
queue<int> Q;
bool vis[MAXN];

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
	scanf( "%d%d%d", &N1, &N2, &N3 );
	scanf( "%d", &M1 );
	for ( int i = 1; i <= M1; ++i ){
		scanf( "%d%d", &x, &y ); Add( y + N1, x, 1 ); Add( x, y + N1, 0 );
	}
	scanf( "%d", &M2 );
	for ( int i = 1; i <= M2; ++i ){
		scanf( "%d%d", &x, &y ); Add( x + N1 + N2 + N3, y + N1 + N2, 1 ); Add( y + N1 + N2, x + N1 + N2 + N3, 0 );
	}
	S = 0; T = 1 + N1 + N1 + N2 + N3;
	for ( int i = 1; i <= N2; ++i ) Add( S, i + N1, 1 ), Add( i + N1, S, 0 );
	for ( int i = 1; i <= N3; ++i ) Add( i + N1 + N2, T, 1 ), Add( T, i + N1 + N2, 0 );
	for ( int i = 1; i <= N1; ++i ) Add( i, i + N1 + N2 + N3, 1 ), Add( i + N1 + N2 + N3, i, 0 );
	
	int t;
	while( BFS() )
		while( ( t = DFS( S, 0x7f7f7f7f ) ) > 0 ) ans += t;
	printf( "%d\n", ans );
	return 0;
}
```