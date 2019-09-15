---
title: vijos1282 佳佳的魔法照片
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:34:05
comments: true
tags:
 - 排序
 - vijos
keywords: 
description: vijos1282
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/20.jpg
mathjax: true
---

# LINK

[vijos1282](https://vijos.org/p/1282)

# 思路
这题没什么好说的，就是快排。。。
STL 的 sort还是很好用的——
如果想更快的还可以按编号分成10组归并，不过这里没有必要。
具体就不仔细讲了，看代码吧。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 50005

int n, k;
int E[15];
int W[MAXN], D[MAXN];

bool cmp( int x, int y ){
    if ( W[x] == W[y] ) return x < y;
    return W[x] > W[y];
}

int main(){
//    freopen( "mphoto.in", "r", stdin );
//    freopen( "mphoto.out", "w", stdout );
    scanf( "%d%d", &n, &k );
    for ( int i = 1; i <= 10; ++i ) scanf( "%d", &E[i] );
    for ( int i = 1; i <= n; ++i ) scanf( "%d", &W[i] ), D[i] = i;
    sort( D + 1, D + n + 1, cmp );
    for ( int i = 1; i <= n; ++i ) W[D[i]] += E[( i - 1 ) % 10 + 1];
    sort( D + 1, D + n + 1, cmp );
    for ( int i = 1; i <= k; ++i ) printf( "%d%c", D[i], "\n "[i != k] );
    return 0;
}
```