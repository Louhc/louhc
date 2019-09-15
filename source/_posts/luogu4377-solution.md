---
title: 洛谷P4377 [USACO18OPEN]Talent Show 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 10:00:08
comments: true
tags: 
 - 分数规划
 - 背包dp
 - 洛谷
keywords: 
description: 洛谷P4377
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/70.jpg
mathjax: true
---

# LINK

[洛谷P4377](https://www.luogu.org/problem/P4377)

# 思路I

很简单的0/1分数规划与背包DP结合。。。

0/1分数规划老套路（安利[机房巨佬zzq博客](https://www.luogu.org/blog/yestoday/post-01-fen-shuo-gui-hua-yang-xie)_还上了洛谷日报$QAQ$_）——>二分答案，条件$\sum (ti \times Ans - wi) \ge 0$

但是，我们注意到$\sum wi$可能会很大，所以我们不能直接弄动态规划。

我们引入一些贪心的思想。

1. 若$t\times Ans-w\ge 0$直接选来。因为这样只会更优化答案。
2. 若1不满足，若$w\ge W$，只有$t\times Ans - w$最大的有可能成为答案。
3. 若2再不满足，背包DP即可。只要把容量设为$2\times W$即可。（请自行思考

然后就是代码啦

# 代码I

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 255
#define LD long double

int N, W, WW;
int w[MAXN], T[MAXN];
LD f[2005], v;

bool check( LD t ){
	int c(0); LD r(0), tmp(-1e12);
	for ( int i = 1; i <= WW; ++i ) f[i] = -1e12;
	
	for ( int i = 1; i <= N; ++i ){
		v = T[i] - w[i] * t;
		if ( v >= 0 ) c += w[i], r += v;
		else{
			if ( w[i] >= W ){ tmp = max( tmp, v ); continue; }
			for ( int j = WW; j >= w[i]; --j ) f[j] = max( f[j], f[j - w[i]] + v );
		}
	}
	if ( c >= W || r + tmp >= 0 ) return 1;
	for ( int i = W - c; i <= WW; ++i ) if ( f[i] + r >= 0 ) return 1;
	return 0;
}

int main(){
	scanf( "%d%d", &N, &W ); WW = W << 1;
	for ( int i = 1; i <= N; ++i ) scanf( "%d%d", &w[i], &T[i] );
	LD l(0), r(1000000), mid;
	for ( int i = 1; i <= 40; ++i ){
		mid = ( l + r ) / 2;
		if ( check( mid ) ) l = mid;
		else r = mid;
	}
	printf( "%d\n", (int)floor(l * 1000) );
	return 0;
}

```

# 思路II

实际上，好像另一种写法更简洁？容量设为W空间还要更小？（我太弱了QAQ

把状态转移方程式写成+的形式，而不是上面那种-的形式。

只要大于等于W的，都缩成一种状态。这样写好简单呀$QAQ$

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 255
#define LD long double

int N, W;
int w[MAXN], T[MAXN];
LD f[1005], v;

bool check( LD t ){
	for ( int i = 1; i <= W; ++i ) f[i] = -1e12;	
	for ( int i = 1; i <= N; ++i ){
		for ( int j = W; j >= 0; --j ){
			int k( min( j + w[i], W ) );
			f[k] = max( f[k], f[j] + T[i] - w[i] * t );
		}
	}
	return f[W] >= 0;
}

int main(){
	scanf( "%d%d", &N, &W );
	for ( int i = 1; i <= N; ++i ) scanf( "%d%d", &w[i], &T[i] );
	LD l(0), r(1000000), mid;
	for ( int i = 1; i <= 40; ++i ){
		mid = ( l + r ) / 2;
		if ( check( mid ) ) l = mid;
		else r = mid;
	}
	printf( "%d\n", (int)floor(l * 1000) );
	return 0;
}

```