---
title: 洛谷P1402 酒店之王 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:49:37
comments: true
tags: 
 - 网络流
 - 洛谷
keywords: 
description: 洛谷P1402
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/114.jpg
mathjax: true
---

# LINK

[洛谷P1402](https://www.luogu.com.cn/problem/P1402)  

# 思路

> 注意，以下出现的所有边边权皆为1，且其反向边边权为0

我们以房间、菜、人为点建图。
像这样：

![](https://img2018.cnblogs.com/blog/1431616/201812/1431616-20181214131744366-1260908236.png)

S(=0)表示额外建的一个起始点，Ri(=i + n + n)表示第i个房间，Di(=i+n+n+p)表示第i种菜，由于人只有一个，而网络流处理只经过一个点不方便，我们采用一种神奇方法——拆点！也就是说，把一个人看做两个点，要匹配这个人必须经过这个人两点之间的边，这样就可以控制这个人只匹配一次。如图，Pi(=i)、Pi'(=i+n)表示第i个人。

然后建边。如图，将S与所有Ri相连，将所有的Di与T相连，S作为源点，T作为汇点。如果Pi喜欢Rj，就将Pi与Rj相连。如果Pi喜欢Dj，就将Dj与Pi'之间相连。当然，Pi与Pi'之间也要连一条边。

然后就可以套网络最大流辣。最后得出的答案即为满意数。

# 拓展

有一天来了n批客人，每批客人喜欢的菜、房间都相同。第i批客人有gi位客人。其余同原题。

> HINT:我们可以把每批客人当做2个点Pi、Pi'，~~在Pi、Pi'之间连gi条边~~连一条权为gi的边即可。菜、房间每种有多个同理。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define open(s) freopen( s".in", "r", stdin ), freopen( s".out", "w", stdout )
#define MAXN 405
#define MAXM 40005

int n, p, q;
int hd[MAXN], nxt[MAXM << 1], to[MAXM << 1], val[MAXM << 1], tot(1);
int ans, dis[MAXN];
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
	scanf( "%d%d%d", &n, &p, &q );
	S = 0; T = 1 + n + n + p + q;
	for ( int i = 1; i <= n; ++i ) Add( i, i + n, 1 ), Add( i + n, i, 0 );
	for ( int i = 1; i <= p; ++i ) Add( S, i + n + n, 1 ), Add( i + n + n, S, 0 );
	for ( int i = 1; i <= q; ++i ) Add( i + n + n + p, T, 1 ), Add( T, i + n + n + p, 0 );
	
	for ( int i = 1; i <= n; ++i )
		for ( int j = 1; j <= p; ++j ){
			int t; scanf( "%d", &t );
			if ( t ) Add( j + n + n, i, 1 ), Add( i, j + n + n, 0 );
		}
	for ( int i = 1; i <= n; ++i )
		for ( int j = 1; j <= q; ++j ){
			int t; scanf( "%d", &t );
			if ( t ) Add( i + n, j + n + n + p, 1 ), Add( j + n + n + p, i + n, 0 );
		}
	int t;
	while( BFS() )
		while( ( t = DFS( S, 0x7f7f7f7f ) ) > 0 ) ans += t;
	printf( "%d\n", ans );
	return 0;
}

```

# 总结

这类题目如果要用网络最大流解决，一般来说，将“选择者”放中间，并且要拆点，“被选物”放两边，直接与源点、汇点相连。但是这种做法“被选物”不能多于两种。

如果多于两种，要怎么做呢？ 我也不知道 QAQ）求教大佬QAQ