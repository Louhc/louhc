---
title: 洛谷P1198 [JSOI2008]最大数 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:23:47
comments: true
tags: 
 - ST表
 - 线段树
 - 洛谷
keywords: 
description: 洛谷P1198
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/75.jpg
mathjax: true
---

# LINK

[洛谷P1198](https://www.luogu.com.cn/problem/P1198)

# 思路

很明显，这道题的一般思路是线段树。但是，这道题用ST表也可以做！！！

一般来说，我们写ST表都是$f[i][j] =\max\text{ a[i] , a[i+1],······,a[i+j \times 2-1] }$

但是，它是从后插入元素的，如果按照一般思路，正着做的话，最坏情况下就要改$nlgn$ 次，还不如暴力呢！但是，我们可以倒过来$f[i][j] = \max\text{a[i] , a[i-1] ,······, a[i - j \times 2 + 1]}$，这样，每次插入仅需修改$lgn$个元素了。

这种解法十分神奇，结束了“ST表不能修改”的历史。在这道题中，合理地使用ST表还是能得出很简洁的解法的。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 200005
#define LL long long

int M;
LL D;
char opt;
LL t, x;
LL a[MAXN], f[MAXN][20], N;

int main(){
	scanf( "%d%lld", &M, &D );
	while ( M-- ){
		while( ( opt = getchar() ) != 'A' && opt != 'Q' );
		scanf( "%lld", &x );
		if ( opt == 'A' ){
			a[++N] = ( x + t ) % D;
			f[N][0] = a[N];
			for ( int i = 1; ( 1 << i ) <= N; ++i ) f[N][i] = max( f[N][i - 1], f[N - (1 << ( i - 1 ))][i - 1] );
		}else{
			int len = min( x, N ); x = N - len + 1;
			int tt((LL)floor(log(len) / log(2)));
			printf( "%lld\n", ( t = max( f[N][tt], f[x + ( 1 << tt ) - 1][tt] ) ) );
		}

	}
	return 0;
}

```