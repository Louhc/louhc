---
title: 洛谷P1343 地震逃生 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:49:01
comments: true
tags: 
 - 网络流
 - 洛谷
keywords: 
description: 洛谷P1343
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/12.jpg
mathjax: true
---

# LINK

[洛谷P1343](https://www.luogu.org/problem/P1343)

# 思路
很明显，1是源点，n是汇点，直接网络最大流模板即可。
这里数据十分小，用Edmonds-Karp或Dinic都可以。
~~我才不告诉你EK怎么打忘光了~~
用网络流求出了每次能通过的学生数ans后，就可以直接求出分成$floor(frac{ans - 1}x) + 1$次通过。总体来说不是很难，具体看代码QAQ

# 代码
```cpp
#include<bits/stdc++.h>
using namespace std;
#define open(s) freopen( s".in", "r", stdin ), freopen( s".out", "w", stdout )
#define MAXN 205
#define MAXM 4005
//建双向边要开两倍别忘了

int n, m, X, ans;//小写的x会冲突（下5行） QAQ 只好改大写
int hd[MAXN], nxt[MAXM], to[MAXM], val[MAXM], tot(1);//MAXN、MAXM别看错了 ~~我才不告诉你为了这个我错了好几遍~~
//还有tot要赋值为1**千万**别忘了 ~~我又为这个调试了好久~~（不用成对存储的请忽略这句话）
int dis[MAXN];
queue<int> Q;
int S, T;
int x, y;

void Add( int x, int y, int z ){nxt[++tot] = hd[x]; hd[x] = tot; to[tot] = y; val[tot] = z;}//链式前向星万岁！~~虽然邻接矩阵也可以~~

bool BFS(){//分层
	while( !Q.empty() ) Q.pop();//清空queue 懒得手打队列QAQ
	memset( dis, 0, sizeof dis );//初始化别忘了
	Q.push(S); dis[S] = 1;
	while( !Q.empty() ){
		x = Q.front(); Q.pop();
		for ( int i = hd[x]; i; i = nxt[i] ){
			if ( val[i] && !dis[to[i]] ){
				dis[to[i]] = dis[x] + 1;
				Q.push( to[i] );
				if ( to[i] == T ) return 1;
			}
		}
	}
	return 0;
}

int DFS( int x, int fl ){
	if ( x == T ) return fl;
	int res(fl), k;
	for ( int i = hd[x]; i && res; i = nxt[i] ){
		if ( ( dis[to[i]] == dis[x] + 1 ) && val[i] ){
			k = DFS( to[i], min( res, val[i] ) );
			if ( !k ) dis[to[i]] = 0; //剪枝！ 不能再增广的点去掉！
			val[i] -= k; val[i^1] += k; res -= k; //成对存储
		}
	}
	return fl - res;
}

int main(){
	scanf( "%d%d%d", &n, &m, &X );
	S = 1; T = n;
	for ( int i = 1; i <= m; ++i ){
		int x, y, z; scanf( "%d%d%d", &x, &y, &z );
		Add( x, y, z ); Add( y, x, 0 );
	}
	
	int t;
	while( BFS() ) while( ( t = DFS( S, INT_MAX ) ) > 0 ) ans += t;
	
	if ( ans ) printf( "%d %d\n", ans, ( X - 1 ) / ans + 1 );//同上
	else printf( "Orz Ni Jinan Saint Cow!\n" ); //这是谁？
	return 0;
}
```