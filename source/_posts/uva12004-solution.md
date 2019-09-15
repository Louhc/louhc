---
title: uva12004 Bubble Sort 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:46:32
comments: true
tags:
 - 期望概率
 - uva
keywords:
description: uva12004
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/64.jpg
mathjax: true
---

# LINK

[UVA12004](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=242&page=show_problem&problem=3155)
[luogu remote judge UVA12004](https://www.luogu.org/problem/UVA12004)

# 思路

一句话题意：求长度为n的排列的期望逆序对数。
很简单，$f(n)=f(n-1)+\frac{n-1}2=\frac{n\times(n-1)}4，f(1)=0$。
为什么呢？假设把$n$插入长度$(n-1)$的排列，有$n$种方法。期望增加的逆序对数就是$\frac{1+2+...n-1}n=\frac{n\times (n-1)}{2n}=\frac{n-1}2$
所以$f(n)=f(n-1)+\frac{n-1}2$
很简单吧？别忘了开long long

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define LL long long

int T, i;
LL n;

int main(){
	scanf( "%d", &T );
	for ( int i = 1; i <= T; ++i ){
		scanf( "%lld", &n );
		n = n * ( n - 1 ) / 2;
		if ( n & 1 ) printf( "Case %d: %lld/2\n", i, n );
		else printf( "Case %d: %lld\n", i, n / 2 );
	}
	return 0;
}

```