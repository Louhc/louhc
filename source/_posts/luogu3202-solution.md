---
title: 洛谷P3202 [HNOI2010]弹飞绵羊 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:54:56
comments: true
tags: 
 - 分块
 - 洛谷
keywords: 
description: 洛谷P3202
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/110.jpg
mathjax: true
---

# LINK

[洛谷P3203](https://www.luogu.org/problem/P3203)

# 思路

我们运用分块思想，把序列分成$\sqrt{n}$块，每个点$i$计录两个值$c[i],s[i]$，表示再弹$s[i]$次第一次落到该块外，且落到$c[i]$的位置。预处理是$O(N)$的，每次修改、查询都是$O(\sqrt N)$的，所以复杂度是正确的（虽然8e7有点悬）。具体看代码。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 200005

int N, M;
int a[MAXN], b[MAXN], n;
int c[MAXN], s[MAXN];

int main(){
	scanf( "%d", &N ); n = (int)sqrt(N);
	for ( int i = 1; i <= N; ++i ) scanf( "%d", &a[i] ), b[i] = ( i - 1 ) / n + 1;//记录每个点所属的块
	for ( int i = N; i >= 1; ){//倒序复杂度是O(N)，正序是O(n^1.5)
		int t(b[i]), j(i);
		while( b[j] == t ){
			if ( j + a[j] > i ) c[j] = j + a[j], s[j] = 1;//一次就弹出该块
			else c[j] = c[j + a[j]], s[j] = s[j + a[j]] + 1;//递推思想
			j--;
		}
		i = j;
	}
	
	int op, x, y; scanf( "%d", &M );
	while ( M-- ){
		scanf( "%d%d", &op, &x ); x++;
		if ( op & 1 ){
			int ans(0);
			for ( int i = x; i <= N; ) ans += s[i], i = c[i];//只要一直弹即可。每次弹都会经过一整个块，因此每次复杂度为O(N^0.5)
			printf( "%d\n", ans );
		}else{
			scanf( "%d", &y );
			a[x] = y; int t(b[x]), j(x);

			while( b[j] == t ){//只要修改该块内即可，复杂度也为O(N^0.5)
				if ( j + a[j] > x ) c[j] = j + a[j], s[j] = 1;
				else c[j] = c[j + a[j]], s[j] = s[j + a[j]] + 1;
				j--;
			}
		}
	}
	return 0;
}
```