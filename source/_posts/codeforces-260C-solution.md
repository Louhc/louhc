---
title: codeforces260C Balls and Boxes 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:13:36
comments: true
tags:
 - 贪心
 - codeforces
keywords: 
description: codeforces260C
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/54.jpg
mathjax: true
---

# LINK

[codeforces260C](http://codeforces.com/problemset/problem/260/C)
[luogu remote judge CF260C](https://www.luogu.org/problemnew/show/CF260C)

# 思路

由于一开始盒子$i$被取空，最后盒子$i$中球的个数肯定是最少的（当然也有可能有的盒子球的个数和它一样少，但绝对不可能比它还小）。所以，找出最小值$t$，所有元素先减去$t$。如果不考虑最后一个放的位置$x$，直接任意选一个$0$变成$t\times N$即可，但是若要考虑$x$，我们可以从$x$不断向前枚举，直至找到第一个$0$。具体处理过程请看代码。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 100005
#define LL long long

int N, x;
LL a[MAXN], t(0x7f7f7f7f7f7f7f7f);

void pre( int &x ){//找前一个元素
	x--; if ( x == 0 ) x = N;
}

int main(){
	scanf( "%d%d", &N, &x );
	for ( int i = 1; i <= N; ++i ) scanf( "%I64d", &a[i] ), t = min( t, a[i] );//最小值
	for ( int i = 1; i <= N; ++i ) a[i] -= t;//先减去t
	t *= N;
	while( a[x] ) a[x]--, t++, pre(x);//a序列中减去后t要加上
	a[x] = t;
	for ( int i = 1; i <= N; ++i ) printf( "%I64d ", a[i] );
	return 0;
}
```

