---
title: loj6278 数列分块入门 2 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:05
comments: true
tags: 
 - 分块
 - loj
keywords: 
description: loj6278
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/8.jpg
mathjax: true
---

# LINK

[loj6278](https://loj.ac/problem/6278)

# 题意概括

区间加法,区间询问小于一个数的个数.

# 正题

对于每个块,除原数组之外用一个vector来有序地存储所有数.当区间加时,对于每个完整块维护共同加数,对于不完整的块直接暴力加上再重新排序.当询问时,对于每个完整块在vector中二分,对于不完整的,直接暴力计数.

# 代码

```cpp
#include<cstdio>
#include<vector>
#include<algorithm>
#include<cmath>
using namespace std;
#define MAXN 50005

int n, m, a[MAXN], p[MAXN], b[500], mm;
vector<int> v[500];
int opt, l, r, c;

int EF( vector<int> vec, int x ){//自力更生,手打二分
	int l, r, mid, ans(-1);
	l = 0; r = vec.size() - 1;
	while( l <= r ){
		mid = ( l + r ) >> 1;
		if ( vec[mid] < x ){
			ans = mid;
			l = mid + 1;
		}
		else r = mid - 1;
	}
	return ans + 1;
}

int query( int l, int r, int c ){
	int ans(0);
	
	if ( p[l] == p[r] ){
		for ( int i = l; i <= r; ++i )
			if ( a[i] + b[p[l]] < c ) ans++;
		return ans;
	}
	
	for ( int i = l; p[i] == p[l]; ++i )
		if ( a[i] + b[p[i]] < c ) ans++;
	for ( int i = r; p[i] == p[r]; --i )
		if ( a[i] + b[p[i]] < c ) ans++;
	for ( int i = p[l] + 1; i <= p[r] - 1; ++i )
		ans += EF( v[i], c - b[i] );
	return ans;
}

void re( int x ){
	v[x].clear();
	int be(( x - 1 ) * m + 1);
	for ( int i = be; p[i] == p[be]; i++ ) v[x].push_back( a[i] );
	sort( v[x].begin(), v[x].end() );
}

void Add( int l, int r, int c ){
	if ( p[l] == p[r] ){
		for ( int i = l; i <= r; ++i ) a[i] += c;
		re( p[l] ); return; 
	}
	
	for ( int i = l; p[i] == p[l]; ++i ) a[i] += c;
	re(p[l]);//重排.实际上可以归并排序,或者要用时再临时排序,这里偷了懒QAQ
	for ( int i = r; p[i] == p[r]; --i ) a[i] += c;
	re(p[r]);
	for ( int i = p[l] + 1; i < p[r]; ++i ) b[i] += c;
}

int main(){
	scanf( "%d", &n ); m = (int)sqrt(n);
	
	for ( int i = 1; i <= n; ++i ) p[i] = ( i - 1 ) / m + 1, mm = p[i];
	for ( int i = 1; i <= n; ++i ) scanf( "%d", &a[i] );
	for ( int i = 1; i <= n; ++i ) v[p[i]].push_back(a[i]);
	for ( int i = 1; i <= mm; ++i ) sort( v[i].begin(), v[i].end() );
	
	for ( int i = 1; i <= n; ++i ){
		scanf( "%d%d%d%d", &opt, &l, &r, &c );
		if ( opt ) printf( "%d\n", query( l, r, c * c ) );
		else Add( l, r, c );
	}
	return 0;
}
```

# 总结

比起其他算法老长老长的代码,分块算法的灵活在本题中得到体现QAQ