---
title: bzoj4716 假摔 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 07:47:18
comments: true
tags:
 - 二分答案
 - bzoj
keywords: 
description: bzoj
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/29.jpg
mathjax: true
---

# LINK

[bzoj4716](https://www.lydsy.com/JudgeOnline/problem.php?id=4716)

# 思路

这道题可以暴力求解。
先二分火力,然后只要判断是否满足即可。
check函数里面可以直接四重循环枚举矩阵的两个端点,由于矩阵中没有负数,装甲值是满足单调性的,不满足时可以直接退出该层循环。
然后就可以做到每次都能找到一个满足条件的矩阵,所以事实上一旦满足条件就退出就只用做k次了。
然后外层循环是$n \times m$的,于是这样看似十分暴力的做法复杂度只有$O((n\times m + k)lg(maxans) )$,完全可以轻松水过此题。
(还有前缀和优化是少不了的。。。)

# 代码

```cpp
#pragma GCC optimize("Ofast")

#include<cstdio>
#include<cctype>
#include<ctime>
#include<cmath>
#define u32 unsigned
#define i64 long long
#define u64 unsigned long long
#define f80 long double
#define rgt register
#define getchar() ( p1 == p2 && ( p2 = bf + fread( bf, 1, 1 << 21, stdin ), p1 = bf ) == p2 ? EOF : *p1++ )
using namespace std;
#define MAXN 1005

char bf[1 << 21], *p1, *p2;
template<typename T>
inline void read( rgt T &x ){ x = 0; rgt char t, flg(0);
    for ( t = getchar(); !isdigit(t); t = getchar() ) flg = t == '-';
    for ( ; isdigit(t); t = getchar() ) x = x * 10 + ( t & 15 ); x = flg ? -x : x;
}

clock_t __t_bg, __t_ed;

int n, m, xm, ym, k, xmin, a[MAXN][MAXN];
int sum( int i, int j, int x, int y ){ return --i, --j, a[x][y] + a[i][j] - a[x][j] - a[i][y]; }

inline char check( int s ){
    rgt int tot(0);
    for ( rgt int i = 1, I = xm; I <= n; ++i, ++I )
        for ( rgt int j = 1, J = ym; J <= m; ++j, ++J )
            for ( rgt int x = I; x <= n && sum( i, j, x, J ) < s; ++x )
                for ( rgt int y = J; y <= m && sum( i, j, x, y ) < s; ++y )
                    if ( ++tot >= k ) return 1;
    return 0;
}

signed main(){
//  freopen( "data", "r", stdin );
    __t_bg = clock();
    read(n), read(m), read(xm), read(ym), read(k);
    for ( rgt int i = 1; i <= n; ++i )
        for ( rgt int j = 1; j <= m; ++j )
            read(a[i][j]), a[i][j] += a[i - 1][j] + a[i][j - 1] - a[i - 1][j - 1];
    rgt int l(1), r(a[n][m]), mid, ans(0);
    while( l <= r ) mid = ( l + r ) >> 1, check(mid) ? r = mid - 1, ans = mid : l = mid + 1;
    printf( "%d\n", ans );
    __t_ed = clock(), fprintf( stderr, "time: %.5lfs\n", double( __t_ed - __t_bg ) / CLOCKS_PER_SEC );
    return 0;
}
```