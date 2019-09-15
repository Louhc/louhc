---
title: 洛谷P1640 [SCOI2010]连续攻击游戏 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-08-03 09:41:53
comments: true
tags: 
 - 二分图
 - 洛谷
keywords: 
description: 洛谷P1640
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/92.jpg
mathjax: true
---

# LINK

[洛谷P1640](https://www.luogu.org/problem/P1640)

# 思路

建二分图,属性和装备为左右部点.  
每件装备能与其两种属性匹配.  
跑匈牙利,匹配失败时直接停止.  

# 代码

```cpp
#include<bits/stdc++.h>
#define u32 unsigned
#define i64 long long
#define u64 unsigned long long
#define f80 long double
#define rgt register
#define fp(i,b,e) for ( int i(b), I(e); i <= I; ++i )
#define fd(i,b,e) for ( int i(b), I(e); i >= I; --i )
#define go(i,b) for ( int i(b), v(to[i]); i; v = to[i = nxt[i]] )
#define getchar() ( p1 == p2 && ( p2 = bf + fread( bf, 1, 1 << 21, stdin ), p1 = bf, p1 == p2 ) ? EOF : *p1++ )
using namespace std;
#define MAXN 10005
#define MAXM 2000005

char bf[1 << 21], *p1, *p2;
template<typename T>
inline void read( rgt T &x ){ x = 0; rgt char t, flg(0);
	for ( t = getchar(); !isdigit(t); t = getchar() ) flg = t == '-';
	for ( ; isdigit(t); t = getchar() ) x = x * 10 + ( t & 15 ); x = flg ? -x : x;
}

int N, hd[MAXN], nxt[MAXM], to[MAXM], tot;
bool vis[MAXN]; int mc[MAXM];

inline void addedge( int u, int v ){
	nxt[++tot] = hd[u], hd[u] = tot, to[tot] = v;
}

bool DFS( int u ){
	if ( vis[u] ) return 0;
	vis[u] = 1;
	go(i, hd[u]){
		if ( !mc[v] || DFS(mc[v]) )
			return mc[v] = u, 1;
	} return 0;
}

signed main(){
	scanf( "%d", &N ); int x, y;
	fp( i, 1, N ) scanf( "%d%d", &x, &y ), addedge( x, i ), addedge( y, i );
	fp( i, 1, 10000 ) if ( ( memset( vis, 0, sizeof vis ), !DFS(i) ) ) return printf( "%d\n", i - 1 ), 0;
	printf( "10000\n" );
	return 0;
}
```