---
title: vijos1285 佳佳的魔法药水
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:31:27
comments: true
tags:
 - 最短路
 - vijos
keywords:
description: vijos1285
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/26.jpg
mathjax: true
---

# LINK

[vijos1285](https://vijos.org/p/1285)

# 思路

这道题，我扪可以使用类似于最短路的方法做。
与最短路类似,我们引入蓝点/白点的概念,即确定一些药品的最佳购买方案,并用它们来优化其他药品的方案.
很明显,目前购买价格(或制取价格)最低的是不可能再优化的,因此把它的价格确定,优化它能优化的所有方案.
然后继续以这种方式确定药水的最小购买(制取)价格,最后得到的就是每个药品的最低价.
由于数据范围较小,这里不必用堆优化,直接暴力就可以了.如果实在要卡你,就使用一个堆优化就好了.
至于方案数,可以再用一个数组记录,与最短路计数差不多,只要注意运用加法原理与乘法原理,这里不再赘述.
还有注意开long long(似乎不开也可以过，保险起见还是开起来吧).

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005
#define INF 1000000000000000000
#define LL long long
typedef pair<int, int> pi;

struct ppp{
    int x, y, z;
};

ppp make( LL x, LL y, LL z ){
    ppp t; t.x = x; t.y = y; t.z = z;
    return t;
}

int N;
int m[MAXN][MAXN];
bool vis[MAXN];
LL dis[MAXN], s[MAXN];

void Dij(){
    for ( int i = 0; i < N; ++i ){
        int t(-1);
        for ( int j = 0; j < N; ++j ) if ( !vis[j] && ( t == -1 || dis[t] > dis[j] ) ) t = j;
        if ( t == -1 ) return;
        vis[t] = 1;
        for ( int j = 0; j < N; ++j )
            if ( m[t][j] < N && vis[t] && vis[j] ){
                if ( dis[m[t][j]] > dis[t] + dis[j] ){
                    dis[m[t][j]] = dis[t] + dis[j]; s[m[t][j]] = s[t] * s[j];
                }else if ( dis[m[t][j]] == dis[t] + dis[j] ) s[m[t][j]] += s[t] * s[j];
            }
    }
}

int main(){
    scanf( "%d", &N );
    for ( int i = 0; i < N; ++i ) scanf( "%lld", &dis[i] ), s[i] = 1;
    int x, y, z;
    memset( m, 0x3f, sizeof m );
    while( ~scanf( "%d%d%d", &x, &y, &z ) ) m[x][y] = m[y][x] = z;
    Dij();
    printf( "%lld %lld\n", dis[0], s[0] );
    return 0;
}
```