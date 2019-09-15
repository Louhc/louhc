---
title: vijos1283 佳佳的魔杖
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:27:10
comments: true
tags:
 - 线性dp
 - vijos
keywords:
description: vijos1283
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/52.jpg
mathjax: true
---

# LINK

[vijos1283](https://vijos.org/p/1283)

# 思路

这是DP题,$f[i][j]$ 表示左端点不超过i且右端点不超过j的最优方案.
很明显转移方程之一是$f[i][j] = max( f[i - 1][j], f[i][j - 1] );$
还有如果长度满足,可以$f[i][j] = max( f[i][j], f[i - 1][j - 1] + 得到魔力值 );$
话不多说,上代码——

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005
#define LL long long

int n, lo, hi;
LL L[MAXN], M[MAXN];
LL f[MAXN][MAXN];

int main(){
    scanf( "%d%d%d", &n, &lo, &hi );
    for ( int i = 1; i <= n; ++i ) scanf( "%lld", &L[i] ), L[i] += L[i - 1];
    for ( int i = 1; i <= n; ++i ) scanf( "%lld", &M[i] ), M[i] += M[i - 1];
    for ( int i = 1; i <= n; ++i )
        for ( int j = 1; j <= i; ++j ){
            f[i][j] = max( f[i - 1][j], f[i][j - 1] );
            if ( L[i] - L[j - 1] >= lo && L[i] - L[j - 1] <= hi ) f[i][j] = max( f[i][j], f[i - 1][j - 1] + M[i] - M[j - 1] );
        }
    printf( "%lld", f[n][n] );
    return 0; 
}
```