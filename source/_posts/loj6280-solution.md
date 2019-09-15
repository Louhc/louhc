---
title: loj6280 数列分块入门 4 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:15
comments: true
tags: 
 - 分块
 - loj
keywords: 
description: loj6280
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/31.jpg
mathjax: true
---

# LINK

[loj6280](https://loj.ac/problem/6280)

# 题意概括

区间加法,区间求和.

# 写在前面

这题...也与分块1如出一辙...

# 正题

当有修改时,对于完整的块,直接维护一个数组$v$记录整个块加过的数(每块共同的加数)与s记录每个块的和(不算共同加数),不完整的就直接暴力在原数组$a$上直接加,并且别忘了给$s$也加上.询问时,对于不完整的块,直接暴力加(别忘了共同加数),完整的块对于每个区间$ans$加上(区间和 + 共同加数$\times$大小)就可以了.

# 代码

```cpp
#include<cstdio>
#include<cmath>
using namespace std;
#define MAXN 50005
#define LL long long

int n, t, m;
LL a[MAXN], p[MAXN], v[500], s[500];
LL opt, l, r, c;

void Add( int l, int r, int c ){
	if ( p[l] == p[r] ){
		for ( int i = l; i <= r; ++i ) a[i] += c, s[p[i]] += c;
		return;
	}
	for ( int i = l; p[l] == p[i]; ++i ) a[i] += c, s[p[i]] += c;
	for ( int i = r; p[r] == p[i]; --i ) a[i] += c, s[p[i]] += c;
	for ( int i = p[l] + 1; i < p[r]; ++i ) v[i] += c;
}

LL query( int l, int r, LL c ){
	if ( p[l] == p[r] ){
		LL ans(0);
		for ( int i = l; i <= r; ++i ) ans += ( a[i] + v[p[i]] ) % c, ans %= c;
		return ans;
	}
	int ans(0);
	for ( int i = l; p[i] == p[l]; ++i ) ans += ( a[i] + v[p[i]] ) % c, ans %= c;
	for ( int i = r; p[i] == p[r]; --i ) ans += ( a[i] + v[p[i]] ) % c, ans %= c;
	for ( int i = p[l] + 1; i < p[r]; ++i ) ans += ( s[i] + v[i] * m ) % c, ans %= c;
	return ans % c;
}

int main(){
	scanf( "%d", &n ); m = (int)sqrt(n);
	for ( int i = 1; i <= n; ++i )
		scanf( "%lld", &a[i] ), p[i] = ( i - 1 ) / m + 1, s[p[i]] += a[i];
	for ( int i = 1; i <= n; ++i ){
		scanf( "%d%lld%lld%lld", &opt, &l, &r, &c );
		if ( opt ) printf( "%lld\n", query( l, r, c + 1 ) );
		else Add( l, r, c );
	}
}
```