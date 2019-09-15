---
title: uva1185 Big Number 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:38:07
comments: true
tags:
 - 数学,数论
 - uva
keywords:
description: uva1185
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/15.jpg
mathjax: true
---

# LINK

[UVA1185](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=246&page=show_problem&problem=3626)
[luogu remote judge UVA1185](https://www.luogu.org/problemnew/show/UVA1185)

# 思路

我们考虑用log解决。
$log_{10}ab=log_{10}a+log_{10}b$
因此$log_{10}n!=log_{10}1+log_{10}2+……+log_{10}n$
$floor(log_{10}n!)+1$即为$n!$的位数。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 10000005
#define LD long double

int f[MAXN];
LD c;
int n, t;

int main(){
	for ( int i = 1; i <= 10000000; ++i ){
		c += log10(i);
		f[i] = (int)floor(c) + 1;
	}
	scanf( "%d", &n );
	while( n-- ){
		scanf( "%d", &t );
		printf( "%d\n", f[t] );
	}
	return 0;
}

```