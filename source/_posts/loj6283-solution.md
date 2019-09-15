---
title: loj6283 数列分块入门 7 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:30
comments: true
tags: 
 - 分块
 - loj
keywords: 
description: loj6283
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/39.jpg
mathjax: true
---

# LINK

[loj6283](https://loj.ac/problem/6283)

# 题意概括

区间乘法,区间加法,单点询问.

# 写在前面

写过线段树模板2的童鞋应该很清楚了吧QAQ

# 正题

我们把一个数表示为 $a_i\times tg2_{b_i} + tg1_{b_i}$.$tg2$表示乘法标记,$tg1$表示加法标记.

对于不完整的块,直接$a_i=a_i\times tg2_{b_i} + tg1_{b_i}$ 将这个块的所有元素都还原,也就是将该块的标记下传.

对于完整的块

​* 乘法:$(a_i\times tg2_{b_i}+tg1_{b_i})\times c=a_i\times(tg2_{b_i}\times c)+(tg1_{b_i}\times c)$也就是说,将$tg1$,$tg2$都乘c就可以了

​* 加法:$(a_i\times tg2_{b_i}\times tg1_{b_i})+c=a_i\times tg2_{b_i}+(tg1_{b_i}+c)$ 也就是将$tg1$加上$c$

然后就很清楚了ヾ(ｏ･ω･)ﾉ

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005
#define mod(x) (1ll * x) % 10007

int n, d;
int a[MAXN], b[MAXN], tg1[500], tg2[500];

inline void Push( int wh ){
	for ( int i = ( wh - 1 ) * d + 1; i <= wh * d; ++i ) a[i] = mod( 1ll * a[i] * tg2[wh] + tg1[wh] );
	tg1[wh] = 0; tg2[wh] = 1;
}

void Add( int l, int r, int c ){
	if ( b[l] == b[r] ){
		Push(b[l]);
		for ( int i = l; i <= r; ++i ) a[i] = mod( a[i] + c );
		return;
	}
	Push(b[l]);
	for ( int i = l; b[i] == b[l]; ++i ) a[i] = mod( a[i] + c );
	Push(b[r]);
	for ( int i = r; b[i] == b[r]; --i ) a[i] = mod( a[i] + c );
	for ( int i = b[l] + 1; i <= b[r] - 1; ++i ) tg1[i] = mod( tg1[i] + c );
}

void Mul( int l, int r, int c ){
	if ( b[l] == b[r] ){
		Push(b[l]);
		for ( int i = l; i <= r; ++i ) a[i] = mod( a[i] * c );
		return;
	}
	Push(b[l]);
	for ( int i = l; b[i] == b[l]; ++i ) a[i] = mod( a[i] * c );
	Push(b[r]);
	for ( int i = r; b[i] == b[r]; --i ) a[i] = mod( a[i] * c );
	for ( int i = b[l] + 1; i <= b[r] - 1; ++i ) tg1[i] = mod( tg1[i] * c ), tg2[i] = mod( tg2[i] * c );
}

int main(){
	scanf( "%d", &n );
	d = sqrt(n);
	for ( int i = 1; i <= n; ++i ){
		scanf( "%d", &a[i] );
		b[i] = ( i - 1 ) / d + 1;
	}
	for ( int i = 1; i <= b[n]; ++i ) tg1[i] = 0, tg2[i] = 1;
	for ( int i = 1; i <= n; ++i ){
		int opt, l, r, c;
		scanf( "%d%d%d%d", &opt, &l, &r, &c );
		if ( opt == 0 ) Add( l, r, c );
		if ( opt == 1 ) Mul( l, r, c );
		if ( opt == 2 ) printf( "%d\n", mod(a[r] * tg2[b[r]] + tg1[b[r]]) );
	}
	return 0;
}
```

# 总结

有多种操作时注意如何合并.