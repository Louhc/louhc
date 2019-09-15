---
title: 洛谷P4035 [JSOI2008]球形空间产生器 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:57:59
comments: true
tags: 
 - 高斯消元
 - 洛谷
keywords: 
description: 洛谷P4035
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/61.jpg
mathjax: true
---

# LINK

[洛谷P4035](https://www.luogu.org/problem/P4035)

# 思路

我们可以列出等式：($o_i$表示圆心坐标)

$$\sum(a_i-o_i)^2=R^2$$

$$\sum(b_i-o_i)^2=R^2$$

$$\sum(c_i-o_i)^2=R^2$$

...

将式子全部展开。以第一个为例。

$$\sum(a_i-o_i)^2=\sum(a_i^2-2a_io_i+o_i^2)$$

$$\therefore \sum(2a_io_i)=\sum(a_i^2)-(R^2-\sum o_i)$$

对于第$i$个式子($1\le i\le N$)，将其与$i+1$个式子相减(但是由于某种不可抗拒的力量，我代码中写成了第$i+1$个式子减第$i$个式子，但是效果是一样的，只不过两边都取反而已)。以第1个与第2个为例。（wow，$-(R^2-\sum o_i)$相互抵消啦

$$\sum(2(a_i-b_i)o_i)=\sum(a^2-b^2)$$

这样就得到了$N$个$N$元一次方程。

然后？高斯消元！$o_i$就是$N$个未知数，$2(a_i-b_i)$就是系数，解$N$元一次方程组即可。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 15
#define LD long double

int N;
LD a[MAXN][MAXN], b[MAXN][MAXN];

int main(){
	scanf( "%d", &N );
	for ( int i = 1; i <= N + 1; ++i )
		for ( int j = 1; j <= N; ++j )
			scanf( "%Lf", &a[i][j] );
	
	for ( int i = 1; i <= N; ++i )
		for ( int j = 1; j <= N; ++j )
			b[i][j] = 2 * ( a[i + 1][j] - a[i][j] ), b[i][N + 1] += a[i + 1][j] * a[i + 1][j] - a[i][j] * a[i][j];
	
	
	for ( int i = 1; i <= N; ++i )
		for ( int j = i + 1; j <= N; ++j ){
			LD t( b[j][i] / b[i][i] );
			for ( int k = i; k <= N + 1; ++k ) b[j][k] -= t * b[i][k];
		}
		
	for ( int i = N; i >= 1; --i ){
		b[i][N + 1] /= b[i][i];
		for ( int j = i - 1; j >= 1; --j ) b[j][N + 1] -= b[i][N + 1] * b[j][i];
	}
	for ( int i = 1; i <= N; ++i ) printf( "%.3Lf ", b[i][N + 1] );
	return 0;
}

```

