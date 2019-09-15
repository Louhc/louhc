---
title: codeforces1108F MST Unification 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:00:21
comments: true
tags:
 - 最小生成树
 - 最近公共祖先
 - codeforces
keywords: 
description: codeforces1108F
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/83.jpg
mathjax: true
---

# LINK

[codeforces1108F MST Unification](http://codeforces.com/problemset/problem/1108/F)
[luogu remote judge CF1108F](https://www.luogu.org/problem/CF1108F)

# 思路

先求出最小生成树,对于那些没被选中的边$(u,v)$,我们设最小生成树中$u$到$v$的路径为$S$,由于要保证最小生成树唯一确定,我们要使$(u,v)$不能替换$S$路径中的任何一条边.因此,求出最小生成树中$(u,v)$路径中边权最大的边$e$。只要让这条边的边权$>v_e$即可.可行性与最优性不难证明,请自行思考.
至于怎么求,我们用倍增LCA实现.总的时间复杂度为$O(nlgn)$.

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 200005
#define LL long long

struct edge{
	int x, y, v; bool c;
	inline void input(){ scanf( "%d%d%d", &x, &y, &v ); c = 0; }
	bool operator < ( const edge &t )const{ return v < t.v; }
}a[MAXN];

int N, M;
int fa[MAXN];
int hd[MAXN], nxt[MAXN << 1], to[MAXN << 1], val[MAXN << 1], tot;
int ft[MAXN][20], mx[MAXN][20], dep[MAXN];

void Add( int x, int y, int z ){
	nxt[++tot] = hd[x]; hd[x] = tot; to[tot] = y; val[tot] = z;
	nxt[++tot] = hd[y]; hd[y] = tot; to[tot] = x; val[tot] = z;
}

int find( int x ){ return fa[x] == x ? x : ( fa[x] = find(fa[x]) ); }
//LCA和并查集都不会的去问度娘
void DFS( int x ){
	dep[x] = dep[ft[x][0]] + 1;
	for ( int i = 1; i <= 17; ++i ) ft[x][i] = ft[ft[x][i - 1]][i - 1], mx[x][i] = max( mx[x][i - 1], mx[ft[x][i - 1]][i - 1] );
	for ( int i = hd[x]; i; i = nxt[i] ) if ( to[i] != ft[x][0] ) ft[to[i]][0] = x, mx[to[i]][0] = val[i], DFS(to[i]);
}

int LCA( int x, int y ){
	int ans(INT_MIN);
	if ( dep[x] < dep[y] ) swap( x, y );
	for ( int i = 17; i >= 0; --i ) if ( dep[ft[x][i]] > dep[y] ) ans = max( ans, mx[x][i] ), x = ft[x][i];
	if ( dep[x] > dep[y] ) ans = max( ans, mx[x][0] ), x = ft[x][0];
	for ( int i = 17; i >= 0; --i ) if ( ft[x][i] != ft[y][i] ) ans = max( ans, max( mx[x][i], mx[y][i] ) ), x = ft[x][i], y = ft[y][i];
	if ( x != y ) ans = max( ans, max( mx[x][0], mx[y][0] ) ), x = ft[x][0], y = ft[y][0];
	return ans;
}

int main(){
	scanf( "%d%d", &N, &M );
	for ( int i = 1; i <= M; ++i ) a[i].input(), fa[i] = i;//读入并初始化求最小生成树用的并查集
	sort( a + 1, a + M + 1 );//排序
	int c(0);
	for ( int i = 1; i <= M; ++i ){
		int x(find(a[i].x)), y(find(a[i].y));
		if ( x != y ){
			fa[x] = y; c++; a[i].c = 1; Add( a[i].x, a[i].y, a[i].v );//最小生成树部分。a[i].c表示是否为最小生成树上的边
			if ( c >= N - 1 ) break;//已构成生成树，退出
		}
	}
	ft[1][0] = 1; mx[1][0] = INT_MIN; DFS(1);//深搜预处理ft父亲与最大边权mx
	LL ans(0);//注意开long long
	for ( int i = 1; i <= M; ++i ){
		if ( !a[i].c ){//不是最小生成树上的边，进行处理
			int t(LCA( a[i].x, a[i].y ));
			if ( a[i].v <= t ) ans += t + 1 - a[i].v;//可以替换某一条边，增大它
		}
	}
	printf( "%lld\n", ans );
	return 0;
}

```

