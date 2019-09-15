---
title: vijos1284 佳佳的魔法阵
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:30:45
comments: true
tags:
 - 搜索剪枝
 - vijos
keywords:
description: vijos1284
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/1.jpg
mathjax: true
---

# LINK

[vijos1284](https://vijos.org/p/1284)

# 思路

这是一道搜索+剪枝题。一看到数据范围($n \times m \le 50$)我便兴奋的飞起——暴搜!!!
然而没打完代码模拟赛就结束了。。。。。。
这道题的搜索的方式要一分为二(其实还是有很多重叠部分的)，前 $ \frac{n \times m}{2}$ 一部分，剩下的一部分。

注意两个剪枝

1. 最优化剪枝：很简单，当前最大值比ans大或者等于ans时直接return。
2. 可行性剪枝：接下来仔细讲讲这个吧。

![img](https://images2018.cnblogs.com/blog/1431616/201808/1431616-20180824160830165-899553372.jpg)

蓝色的点表示当前位置，红色的点表示访问过的位置。这种情况(左、右两点不能再访问而上下两点未访问)是一种不可行的情况。

为什么呢？

仔细想一想就能明白了。

以上述情况为例，蓝色点的上方或下方必定会构成死胡同。

像这样：

![img](https://images2018.cnblogs.com/blog/1431616/201808/1431616-20180824161700815-623215301.jpg)

具体自己再理解理解吧。

然后上代码！

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 55

int n, m, k1, k2, ans(0x7f7f7f7f), nn;
int a[MAXN][2];
bool vis[MAXN][MAXN];
int dir[][2] = { 1, 0, 0, 1, -1, 0, 0, -1 };

bool check( int x, int y ){
    if ( vis[x + 1][y] && vis[x - 1][y] && !vis[x][y + 1] && !vis[x][y - 1] ) return 0;
    if ( vis[x][y + 1] && vis[x][y - 1] && !vis[x + 1][y] && !vis[x - 1][y] ) return 0;
    return !vis[x][y];
} 

inline int ABS( int x ){
    return x < 0 ? -x : x; 
}

inline int Get( int x, int y, int a, int b ){
    return k1 * ABS( x - a ) + k2 * ABS( y - b );
} 

void DFS( int x, int y, int wh, int cur ){
    if ( wh <= nn ) a[wh][0] = x, a[wh][1] = y;
    else cur = max( cur, Get( x, y, a[wh - nn][0], a[wh - nn][1] ) );
        
    if ( wh >= n * m ){
        ans = min( ans, cur );
        return;
    }
    if ( cur >= ans ) return;
    
    vis[x][y] = 1;
    for ( int i = 0; i < 4; ++i ){
        int tx( x + dir[i][0] ), ty( y + dir[i][1] );
        if ( !check( tx, ty ) ) continue;
        DFS( tx, ty, wh + 1, cur );
    }
    vis[x][y] = 0;
}

int main(){
    scanf( "%d%d%d%d", &n, &m, &k1, &k2 );
    for ( int i = 0; i <= n + 1; ++i ) vis[i][0] = vis[i][m + 1] = 1;
    for ( int i = 0; i <= m + 1; ++i ) vis[0][i] = vis[n + 1][i] = 1;
    nn = n * m >> 1;
    DFS( 1, m, 1, 0 );
    printf( "%d\n", ans );
    return 0;
}
```