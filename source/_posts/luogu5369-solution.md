---
title: 洛谷P5369 [PKUSC2018]最大前缀和 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-12-05 21:55:12
comments: true
tags: 
 - 期望概率
 - 状态压缩
 - 洛谷
keywords: 
description: 洛谷P5369
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/75.jpg
mathjax: true
---

# LINK

[洛谷P5369](https://www.luogu.com.cn/problem/P5369)
[LOJ6433](https://loj.ac/problem/6433)

# 思路

数据范围这么小肯定是状压没错了.

做这种题一个基本的套路就是枚举所有可能产生贡献的状态,并把所有状态加起来.
从这方面考虑,我们枚举该序列中一些元素构成的集合 $S$ ,计算最大前缀和为这些元素之和的方案数.
要满足最大前缀和为 $sum(S)$ ,需要满足哪些条件呢?首先, $S$ 的最大前缀和为 $sum(S)$,其次, $all-S$ 所有的前缀和都得小于 $0$ ($all$ 表示整个序列,即全集) .
我们记 $S$ 构成的序列中有 $f(S)$ 个序列满足最大前缀和为 $sum(S)$, 有 $g(S)$ 个序列满足所有前缀和小于 $0$,那么答案就是 $\sum_{S}f(S)g(all-S)sum(S)$.

接下来难点就在于转移了.

先考虑 $f(S)$, 枚举序列的第一个元素 $x\in S$.如果 $sum(S-x)<0$,肯定不合题意.因为这样会导致 $x > x + sum(S-x)$.如果序列的最大前缀和不为 $sum(S-x)$, 很明显也不行,因为这样会导致加上 $x$ 后序列的最大前缀和不为 $x+sum(S-x)=sum(S)$.因此,可以写出方程 $f(S)=\sum_{x\in S,sum(S-x)\ge0} f(S-x)$.

然后考虑 $g(S)$,枚举序列的最后一个元素 $x \in S$. 如果 $sum(S)\ge0$, 易得 $g(S)=0$.只需要满足 $x$ 之前的序列满足条件即可,即 $g(S)=\sum_{x \in S} g(S-x)$.

然后就可以愉快地求出答案了.

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define i64 long long
#define f80 long double
#define rgt register
#define fp( i, b, e ) for ( int i(b), I(e); i <= I; ++i )
#define fd( i, b, e ) for ( int i(b), I(e); i >= I; --i )
#define go( i, b ) for ( int i(b), v(to[i]); i; v = to[i = nxt[i]] )
template<typename T> inline bool cmax( T &x, T y ){ return x < y ? x = y, 1 : 0; }
template<typename T> inline bool cmin( T &x, T y ){ return y < x ? x = y, 1 : 0; }

const int _ = 1 << 20, mod = 998244353;
int N, n, f[_], g[_];
i64 s[_];

inline int dec( int x ){ return x >= mod ? x - mod : x; }

signed main(){
    cin >> N, n = (1 << N) - 1, g[0] = 1;
    fp( i, 0, N - 1 ) cin >> s[1 << i], f[1 << i] = 1;
    fp( i, 0, n ) s[i] = s[i & -i] + s[i ^ (i & -i)];
    fp( i, 1, n ){
        if ( s[i] < 0 ){ fp( j, 0, N - 1 ) if ( (i >> j) & 1 ) g[i] = dec(g[i] + g[i ^ (1 << j)]); }
        else{ fp( j, 0, N - 1 ) if ( ~(i >> j) & 1 ) f[i | (1 << j)] = dec(f[i | (1 << j)] + f[i]); }
    } int ans(0); fp( i, 1, n ) ans = (ans + s[i] % mod * f[i] % mod * g[n ^ i] ) % mod;
    printf( "%d\n", (ans + mod) % mod );
    return 0;
}
```