---
title: codeforces1108C Nice Garland 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:00:21
comments: true
tags:
 - 枚举
 - codeforces
keywords: 
description: codeforces1107B
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/57.jpg
mathjax: true
---

# LINK

[codeforces1108C](http://codeforces.com/problemset/problem/1107/C)
[luogu remote judge CF1108C](https://www.luogu.org/problem/CF1108C)

# 思路

我们可以想到，要满足条件，必须要有$s[0]\ne s[1]\ne s[2]$，$s[i]=s[i\% 3]$（很好理解吧？）
所以$s[0],s[1],s[2]$分别选取$'R','G','B'$（可打乱顺序），这可以用全排列解决；然后处理出要修改的个数，记录最优答案即可。时间复杂度为$O(3!\times n)$，稳得很。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 200005

int N;
char s[MAXN], cur[MAXN], ans[MAXN];
int res(INT_MAX);
int m[] = { 1, 2, 3 };
char a1, a2, a3;

int main(){
	scanf( "%d", &N );
	scanf( "%s", s );
	do{
		int c(0);
		a1 = m[0] == 1 ? 'R' : ( m[0] == 2 ? 'G' : 'B' );
		a2 = m[1] == 1 ? 'R' : ( m[1] == 2 ? 'G' : 'B' );
		a3 = m[2] == 1 ? 'R' : ( m[2] == 2 ? 'G' : 'B' );
		for ( int i = 0; i < N; i += 3 ){ cur[i] = a1; if ( s[i] != a1 ) c++; }
		for ( int i = 1; i < N; i += 3 ){ cur[i] = a2; if ( s[i] != a2 ) c++; }
		for ( int i = 2; i < N; i += 3 ){ cur[i] = a3; if ( s[i] != a3 ) c++; }
		if ( c < res ){ res = c; memcpy( ans, cur, sizeof ans ); }
	}while( next_permutation( m, m + 3 ) );
	printf( "%d\n%s\n", res, ans );
	return 0;
}

```

