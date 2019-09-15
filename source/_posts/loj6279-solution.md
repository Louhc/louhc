---
title: loj6279 数列分块入门 3 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:10
comments: true
tags: 
 - 分块
 - loj
keywords: 
description: loj6279
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/15.jpg
mathjax: true
---

# LINK

[loj6279](https://loj.ac/problem/6279)

# 题意概括

区间加法,区间求前驱.

# 写在前面

这题的方法与分块2方法极其类似,建议自行解决.

# 正题

和上一题类似,但是二分不是用来计数的,而是用来求小于c的最大值的.然后对于不完整快,将小于c的值求最大值,再与所有块中二分结果求最大值即可.(其他思路上一篇题解已经讲了,这里不再复述,代码注释也懒得打了,因为比较简单,很容易理解)

# 代码

```cpp
#include<cstdio>
#include<vector>
#include<algorithm>
#include<cmath>
using namespace std;
#define MAXN 100005

int n, m, a[MAXN], p[MAXN], b[1005], mm;
vector<int> v[1005];
int opt, l, r, c;

int EF( vector<int> vec, int x ){
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
	return ans;
}

int query( int l, int r, int c ){
	int ans(-1);
	
	if ( p[l] == p[r] ){
		for ( int i = l; i <= r; ++i )
			if ( a[i] + b[p[l]] < c ) ans = max( ans, a[i] + b[p[l]] );
		return ans;
	}
	
	for ( int i = l; p[i] == p[l]; ++i )
		if ( a[i] + b[p[i]] < c ) ans = max( ans, a[i] + b[p[l]] );
	for ( int i = r; p[i] == p[r]; --i )
		if ( a[i] + b[p[i]] < c ) ans = max( ans, a[i] + b[p[r]] );
	for ( int i = p[l] + 1; i <= p[r] - 1; ++i ){
		int t(EF( v[i], c - b[i] ));
		if ( t >= 0 ) ans = max( ans, v[i][t] + b[i] );
	}
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
	re(p[l]);
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
		if ( opt ) printf( "%d\n", query( l, r, c ) );
		else Add( l, r, c );
	}
	return 0;
}
```

# 总结

分块要触类旁通,充分发挥分块的灵活性.