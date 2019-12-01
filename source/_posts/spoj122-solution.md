---
title: spoj122 STEVE - Voracious Steve 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:03:03
comments: true
tags:
 - 博弈论
 - spoj
keywords:
description: spoj122
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/93.jpg
mathjax: true
---

# LINK

[spoj STEVE](https://www.spoj.com/problems/STEVE/)
[luogu remote judge SP122](https://www.luogu.com.cn/problem/SP122)

# 思路

$a[all][one][tho]$表示总共有$all$个甜甜圈，现在轮到的人已经取了$one$个甜甜圈，另一个人取了$tho$个甜甜圈，现在轮到的人最多能取到的甜甜圈数。
我们用记搜方式实现。
当$all=0$时，直接返回0。
当$all\not=0$时，我们枚举取的个数，从中选出最大值。
一些边界、细节请看代码。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;

int n, m;
int a[205][205][205];

int SG( int all, int one, int tho ){
	if ( all == 0 ) return 0;
	if ( a[all][one][tho] ) return a[all][one][tho];
	int ans(0);//ans为找出的最大值
	for ( int i = 1; i <= min( m, all - one - tho ); ++i ){
		if ( one + tho + i == all ) ans = max( ans, all - SG( tho, 0, 0 ) );//取完了，把没取到最后一个的人的甜甜圈拿来继续取
		else ans = max( ans, all - SG( all, tho, one + i ) );//没取玩，继续取
	}
	return a[all][one][tho] = ans;//记录答案
}

int main(){
	int T;
	scanf( "%d", &T );
	while( T-- ){
		memset( a, 0, sizeof a );//每次的m都不同，所以别忘了初始化
		scanf( "%d%d", &n, &m );
		printf( "%d\n", SG( n, 0, 0 ) );
	}
	return 0;
}
```