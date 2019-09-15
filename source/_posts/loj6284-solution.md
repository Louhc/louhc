---
title: loj6284 数列分块入门 8 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:35
comments: true
tags: 
 - 分块
 - loj
keywords: 
description: loj6284
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/50.jpg
mathjax: true
---

# LINK

[loj6284](https://loj.ac/problem/6284)

# 题意概括

区间修改,区间计数.

# 写在前面

感叹\~ 分块真是玄之又玄|(￣0￣)

# 正题

跟分块7类似的是,这题也运用标记下传.

v数组用来记录一个分块是否都为一个数,f数组来记录如果都为一个数,那么这个数是什么(实际上可以用只一个数组来记录QAQ)

查询时,对于都为一个数的块,直接加上(如果这个数是$c$),顺便把$f$改为$c$,不是的话暴力计数,顺便把$v$改为$1$,$f$改为$c$.

不完整的块稍微麻烦一点,如果$v$为$1$的话先标记下传(都变成$f$的值),然后再修改\~

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005

int n, d;
int a[MAXN], b[MAXN], f[500];
bool v[500];

int FF( int x ){//取出x位置的值
	return v[b[x]] ? f[b[x]] : a[x];
}

int fun( int l, int r, int c ){//计数&修改
	int ans(0);
	for ( int i = l; i <= r && i <= n; ++i ) ans += FF(i) == c, a[i] = c;
	return ans;
}

int Get( int l, int r, int c ){
	int ans(0);
	if ( b[l] == b[r] ){
		if ( v[b[l]] && f[b[l]] == c ) return r - l + 1;
		
		if ( v[b[l]] ){
			fun( ( b[l] - 1 ) * d + 1, l - 1, f[b[l]] );
			fun( r + 1, b[l] * d, f[b[l]] );
		}
		ans = fun( l, r, c );
		v[b[l]] = 0;
		return ans;
	}
	
	if ( v[b[l]] ) fun( ( b[l] - 1 ) * d + 1, l - 1, f[b[l]] );
	ans += fun( l, b[l] * d, c );
	v[b[l]] = 0;
	
	
	if ( v[b[r]] ) fun( r + 1, min( b[r] * d, n ), f[b[r]] );
	ans += fun( ( b[r] - 1 ) * d + 1, r, c );
	v[b[r]] = 0;
	
	for ( int i = b[l] + 1; i <= b[r] - 1; ++i ){
		if ( !v[i] ){
			for ( int j = ( i - 1 ) * d + 1; b[j] == i; ++j ) ans += a[j] == c, a[j] = c;
			v[i] = 1; f[i] = c;
		} else{
			if ( f[i] == c ) ans += d;
			else f[i] = c;
		}
	}
	return ans;
}

int main(){
	scanf( "%d", &n );
	d = (int)sqrt(n);
	for ( int i = 1; i <= n; ++i ){
		scanf( "%d", &a[i] );
		b[i] = ( i - 1 ) / d + 1;
	}
	for ( int i = 1; i <= n; ++i ){
		int l, r, c;
		scanf( "%d%d%d", &l ,&r, &c );
		printf( "%d\n", Get( l, r, c ) );
	}
	return 0;
}
```

# 总结

分块大法好!