---
title: 洛谷P2824 [HEOI2016/TJOI2016]排序 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:52:46
comments: true
tags: 
 - 线段树
 - 二分答案
 - 洛谷
keywords: 
description: 洛谷P2824
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/54.jpg
mathjax: true
---

# LINK

[洛谷P2824](https://www.luogu.com.cn/problem/P2824)

# 思路

线段树神仙题。
~~当然要**离线处理**啦。~~

先二分答案$x$(至于为什么等下说)，然后对所有数进行如下操作：
1. $< x$的，修改成0
2. $\ge x$的，修改成1

然后对0/1串进行排序，最后第$q$位为1的check函数返回true,否则返回false。

为什么这样做是正确的呢？
因为二分找到$ans$的一定是最小的能使第$q$位为1,或者说$check(ans)$为true的数。
也就是说，当取到$ans-1$时，第$q$位为0，$check(ans-1)$返回的肯定是false。
可以看出，$x$取值从$ans-1$转变为$ans$的结果是排序后第$q$位的值，换句话讲就是第$q$位原来就是$ans$。
然后二分+线段树就OK啦。

还有一个问题，对0/1串排序。
这个其实也很简单，直接查询区间内1的个数，区间覆盖即可。
（如果你想用珂朵莉树的话我也没办法qwq

P.S. 这题还可以实现$O(nlgn)$的复杂度qwq,是线段树合并/分裂 + set/线段树，看起来代码有点长qwq.
P.S. 似乎这题桶排暴力都能过，但是还是资瓷正解的啦qwq.

# 代码

```cpp
#include<bits/stdc++.h>
#define u32 unsigned
#define i64 long long
#define u64 unsigned long long
#define f80 long double
#define rgt register
#define getchar() ( p1 == p2 && ( p2 = bf + fread( bf, 1, 1 << 21, stdin ), p1 = bf ) == p2 ? EOF : *p1++ )
using namespace std;
#define MAXN 100005

char bf[1 << 21], *p1, *p2;
template<typename T>
inline void read( rgt T &x ){ x = 0; rgt char t, flg(0);
	for ( t = getchar(); !isdigit(t); t = getchar() ) flg = t == '-';
	for ( ; isdigit(t); t = getchar() ) x = x * 10 + ( t & 15 ); x = flg ? -x : x;
}

clock_t __t_bg, __t_ed;

int N, M, Q, a[MAXN], L[MAXN], R[MAXN], opt[MAXN], ans, x;
int tr[MAXN << 2], tg[MAXN << 2];

#define ls c << 1
#define rs c << 1 | 1
void build( int c, int l, int r ){
	tg[c] = -1;
	if ( l == r ) return tr[c] = a[l] >= x, void();
	int mid((l + r) >> 1);
	build( ls, l, mid ),
	build( rs, mid + 1, r ),
	tr[c] = tr[ls] + tr[rs];
}

inline void tag( int c, int l, int r, int x ){ tr[c] = x ? r - l + 1 : 0, tg[c] = x; }
inline void pushdown( int c, int l, int r ){
	if ( tg[c] != -1 ){
		rgt int mid((l + r) >> 1);
		tag( ls, l, mid, tg[c] ), tag( rs, mid + 1, r, tg[c] ), tg[c] = -1;
	}
}

void cover( int x, int y, char z, int c = 1, int l = 1, int r = N ){
	if ( x <= l && r <= y ) return tag( c, l, r, z );
	if ( x > r || l > y ) return;
	int mid((l + r) >> 1);
	pushdown( c, l, r ),
	cover( x, y, z, ls, l, mid ),
	cover( x, y, z, rs, mid + 1, r ),
	tr[c] = tr[ls] + tr[rs];
}

int get( int c, int l, int r, int x, int y ){
	if ( x <= l && r <= y ) return tr[c];
	if ( x > r || l > y ) return 0;
	int mid((l + r) >> 1);
	pushdown( c, l, r );
	return get( ls, l, mid, x, y ) + get( rs, mid + 1, r, x, y );
}

inline char check(){
	build( 1, 1, N );
	for ( rgt int i = 1, ct1; i <= M; ++i ){
		ct1 = get( 1, 1, N, L[i], R[i] );
		if ( opt[i] ) cover( L[i], L[i] + ct1 - 1, 1 ), cover( L[i] + ct1, R[i], 0 );
		else cover( L[i], R[i] - ct1, 0 ), cover( R[i] - ct1 + 1, R[i], 1 );
	}
	return get( 1, 1, N, Q, Q );
}

signed main(){
	__t_bg = clock(); read(N), read(M);
	for ( rgt int i = 1; i <= N; ++i ) read(a[i]);
	for ( rgt int i = 1; i <= M; ++i ) read(opt[i]), read(L[i]), read(R[i]);
	read(Q); rgt int l(1), r(N);
	while( l <= r ) x = ( l + r ) >> 1, check() ? ans = x, l = x + 1 : r = x - 1;
	printf( "%d\n", ans );
	__t_ed = clock(), fprintf( stderr, "time: %.5lfs\n", double( __t_ed - __t_bg ) / CLOCKS_PER_SEC );
	return 0;
}

```