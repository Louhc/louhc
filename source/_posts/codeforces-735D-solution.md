---
title: codeforces735D Taxes 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:15:47
comments: true
tags:
 - 哥德巴赫猜想
 - codeforces
keywords: 
description: codeforces735D
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/37.jpg
mathjax: true
---

# LINK

[luogu remote judge CF735D](https://www.luogu.com.cn/problem/CF735D)
[codeforces735D](http://codeforces.com/problemset/problem/735/D)

# 思路

神奇的哥德巴赫猜想应用啊！
哥德巴赫猜想虽然还没有被证明~~，但是反正是哥德巴赫说的，错了怪他~~
哥德巴赫猜想的大概就是任一大于2的偶数都可写成两个质数之和。
所以如果N本身是质数(或者是1)，答案就是1啦。
如果N是偶数(>2)，根据哥德巴赫猜想，直接分成两个质数，答案为2。
如果N是奇数(>2,非质数)，先变成3+(N-3)，很明显，(N-3)是>2的偶数，所以答案为1+2=3。
证明没有更优的方案十分easy（其实是我太懒懒得写证明，只会口头证明）

# 代码

```cpp
#include<bits/stdc++.h>
#define u32 unsigned
#define i64 long long
#define u64 unsigned long long
#define f80 long double
#define rgt register
//#define getchar() ( p1 == p2 && ( p2 = bf + fread( bf, 1, 1 << 21, stdin ), p1 = bf ) == p2 ? EOF : *p1++ )
using namespace std;

char bf[1 << 21], *p1, *p2;
template<typename T>
inline void read( rgt T &x ){ x = 0; rgt char t, flg(0);
	for ( t = getchar(); !isdigit(t); t = getchar() ) flg = t == '-';
	for ( ; isdigit(t); t = getchar() ) x = x * 10 + ( t & 15 ); x = flg ? -x : x;
}

clock_t __t_bg, __t_ed;

int N;
char isprime( int x ){
	for ( rgt int i = 2, I((int)sqrt(x)); i <= I; ++i )
		if ( x % i == 0 ) return 0;
	return 1;
}

signed main(){
	__t_bg = clock();
	read(N);
	if ( isprime(N) ) printf( "1\n" );
	else if ( !(N & 1) || isprime(N - 2) ) printf( "2\n" );
	else printf( "3\n" );
	__t_ed = clock(), fprintf( stderr, "time: %.5lfs\n", double( __t_ed - __t_bg ) / CLOCKS_PER_SEC );
	return 0;
}
```