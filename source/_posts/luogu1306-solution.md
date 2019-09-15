---
title: 洛谷P1306 斐波那契公约数 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:47:18
comments: true
tags: 
 - 矩阵加速递推
 - 洛谷
keywords: 
description: 洛谷P1306
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/40.jpg
mathjax: true
---

# LINK

[洛谷P1306](https://www.luogu.org/problem/P1306)

# 思路

初看此题，毫无头绪，其实并不难。

结论很简单，设$f[i]$表示斐波那契数列第$i$个，则有$\gcd(f[i],f[j])=f[\gcd(i,j)]$。

为什么呢？

显然，当$i=j$时，结论成立。

假设$i< j$，我们设$f[i]=a,f[i+1]=b,f[i+2]=a+b...$

很明显，$f[j]=f[j-i]\times b+f[j-i-1]\times a$

因此$\gcd(f[i],f[j])=\gcd(f[i],f[j-i]\times f[i+1]+f[j-i-1]\times f[i])$

即$\gcd(f[i],f[j])=\gcd(f[i],f[j-i]\times f[i+1])$

引理：$\gcd(f[i],f[i+1])=1$

证明：

显然，$\gcd(f[1],f[2])=1$成立。

$\gcd(f[2],f[3])=\gcd(f[2],f[3]-f[2])=\gcd(f[2],f[1])=1$

$\gcd(f[3],f[4])=\gcd(f[3],f[4]-f[3])=\gcd(f[3],f[2])=1$

...

QED.

回到$\gcd(f[i],f[j])=\gcd(f[i],f[j-i]\times f[i+1])$这个式子，因为$\gcd(f[i],f[i+1])=1$，因此$\gcd(f[i],f[j])=\gcd(f[i],f[j-i]\times f[i+1])=\gcd(f[i],f[j-i])$

发现了吧？这和更相减损术的$\gcd(i,j)=\gcd(i,i-j)$蜜汁相似，没错，就是公约数！$\gcd(f[i],f[j])$就等于$f[\gcd(i,j)]$！

然后用矩阵快速幂求出$f[\gcd(n,m)]$即可。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register

int N, M;

int gcd( int x, int y ){ return y ? gcd( y, x % y ) : x; }

int a[3][3], b[3][3], c[3][3];

inline void Times( int a[3][3], int b[3][3] ){
	memset( c, 0, sizeof c );
	for ( int i = 1; i <= 2; ++i )
		for ( int k = 1; k <= 2; ++k )
			for ( int j = 1; j <= 2; ++j )
				c[i][j] = (int)( ( c[i][j] + 1ll * a[i][k] * b[k][j] ) % 100000000 );
}

int main(){
	scanf( "%d%d", &N, &M );
	N = gcd( N, M ) - 2;
	if ( N <= 0 ){ printf( "1\n" ); return 0; }
	a[1][1] = a[1][2] = b[1][1] = b[1][2] = b[2][1] = 1;
	for ( int i = N; i; i >>= 1 ){
		if ( i & 1 ) Times( a, b ), memcpy( a, c, sizeof a );
		Times( b, b ), memcpy( b, c, sizeof b );
	}
	printf( "%d\n", a[1][1] );
	return 0;
}

```