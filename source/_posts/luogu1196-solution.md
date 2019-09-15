---
title: 洛谷P1196 [NOI2002]银河英雄传说 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:22:34
comments: true
tags: 
 - 并查集
 - 洛谷
keywords: 
description: 洛谷P1196
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/85.jpg
mathjax: true
---

# LINK

[洛谷P1196](https://www.luogu.org/problem/P1196)

# 思路

我们很容易想到记录每只船在某处排行位置，然后询问时直接减一减就可以了。就这样，问题转换为如何保留每个数的位置(d[i])。

Hint:注意，为了方便，我们把d[i]记作相对f[i]的位置。

当一列船(记为A 首只船为 a)合并到另一列(记为B 首只船为b)时，我们为了让a直接以b为父亲，我们要记录每一列船的只数(s[i])，将d[a]修改为s[a] + 1(连到最后时rank为原来船数+1)，别忘了修改s[b]与f[a]的值。对于后面父亲不为B的先不管。当寻找祖先时，对于父亲是祖先的船，我们不用修改，因为在合并时已经修改过了，而对于父亲不是祖先的船，我们可以先修改它的父亲(递归进行)，是他的父亲的父亲为它的老祖宗，然后把它的d[i] = d[i] + d[f[i]];这样就把参照物改为他的祖宗，实现了路径压缩。

代码很短，只有一点点。。。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define N 30000

int T;
int f[N + 5], d[N + 5], s[N + 5];

int find( int x ){
	if ( f[x] == x ) return x;
	int fa(find(f[x]));
	d[x] = d[x] + d[f[x]] - 1;
	return f[x] = fa;
}

void Merge( int x, int y ){
	x = find(x); y = find(y);
	if ( x == y ) return;
	f[x] = y; d[x] = s[y] + 1; s[y] += s[x];
}

int main(){
	scanf( "%d", &T );
	for ( int i = 1; i <= N; ++i ) f[i] = i, d[i] = 1, s[i] = 1;
	while( T-- ){
		char t; int x, y;
		while( ( t = getchar() ) != 'M' && t != 'C' );
		scanf( "%d%d", &x, &y );
		if ( t == 'M' ) Merge( x, y );
		else{
			if ( find(x) == find(y) ){
				if ( x == y ) printf("0\n");
				else if ( d[x] > d[y] ) printf( "%d\n", d[x] - d[y] - 1 );
				else printf( "%d\n", d[y] - d[x] - 1 );
			} else printf( "-1\n" );
		}
	}
	return 0;
}
```