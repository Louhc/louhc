---
title: 洛谷P4168 [Violet]蒲公英 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:58:33
comments: true
tags: 
 - 分块
 - 洛谷
keywords: 
description: 洛谷P1080
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/19.jpg
mathjax: true
---

# LINK

[洛谷P4168](https://www.luogu.org/problem/P4168)

# 思路

这道题和 数列分块入门 9 蜜汁相似QAQ。
请自行参照我的 数列分块入门 9 解题报告
这里仅放上代码QAQ——

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 40005

int n, m, T;
int a[MAXN], b[MAXN], c[MAXN];
int d, f[2000][2000];
int s[MAXN];
vector<int> p[MAXN];

int Count( int l, int r, int x ){
	return upper_bound( p[x].begin(), p[x].end(), r ) - lower_bound( p[x].begin(), p[x].end(), l );
}

int Get( int l, int r ){
	if ( b[l] == b[r] ){
		int ans1(0), ans2(0);
		for ( int i = l; i <= r; ++i ){
			int t(Count( l, r, a[i] ));
			if ( t > ans2 ) ans1 = a[i], ans2 = t;
			if ( t == ans2 ) ans1 = min( ans1, a[i] );
		}
		return ans1;
	}
	int ans1(f[b[l] + 1][b[r] - 1]), ans2(Count( l, r, ans1 ));
	for ( int i = l; b[l] == b[i]; ++i ){
		int t(Count( l, r, a[i] ));
		if ( t > ans2 ) ans1 = a[i], ans2 = t;
		if ( t == ans2 ) ans1 = min( ans1, a[i] );
	}
	for ( int i = r; b[r] == b[i]; --i ){
		int t(Count( l, r, a[i] ));
		if ( t > ans2 ) ans1 = a[i], ans2 = t;
		if ( t == ans2 ) ans1 = min( ans1, a[i] );
	}
	return ans1;
}

int main(){
	scanf( "%d%d", &n, &T );
	d = 0;
	while( ( 1 << d ) <= n ) d++;
	d = (int)( n / sqrt( 2 * T * d ) );
	for ( int i = 1; i <= n; ++i ){
		scanf( "%d", &a[i] ); c[i] = a[i]; b[i] = ( i - 1 ) / d + 1;
	}
	sort( c + 1, c + n + 1 );
	m = unique( c + 1, c + n + 1 ) - c - 1;
	for ( int i = 1; i <= n; ++i ) a[i] = lower_bound( c + 1, c + m + 1, a[i] ) - c;
	for ( int i = 1; i <= n; ++i ) p[a[i]].push_back(i);
	
	for ( int i = 1; i <= b[n]; ++i ){
		memset( s, 0, sizeof s );
		int ans1(0), ans2(0);
		for ( int j = ( i - 1 ) * d + 1; j <= n; ++j ){
			s[a[j]]++;
			if ( s[a[j]] == ans2 ) ans1 = min( ans1, a[j] );
			if ( s[a[j]] > ans2 ) ans1 = a[j], ans2 = s[a[j]];
			if ( b[j + 1] != b[j] ) f[i][b[j]] = ans1;
		}
	}
	
	int x(0);
	while( T-- ){
		int l, r;
		scanf( "%d%d", &l, &r );
		l = ( l + x - 1 ) % n + 1; r = ( r + x - 1 ) % n + 1;
		int t(min( l, r )); r = max( l, r ); l = t;
		printf( "%d\n", x = c[Get( l, r )] );
	}
	return 0;
}
```