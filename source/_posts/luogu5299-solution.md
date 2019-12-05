---
title: 洛谷P5299 [PKUWC2018]Slay the Spire 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-12-05 15:30:51
comments: true
tags: 
 - 期望概率
 - 洛谷
keywords: 
description: 洛谷P5299
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/51.jpg
mathjax: true
---

# LINK

[洛谷P5299](https://www.luogu.com.cn/problem/P5299)

# 思路

首先,我们很容易想到一个贪心策略,就是在保证能用至少一张攻击牌的前提下,尽量多选强化牌.当然,要从大选到小. 因为强化牌至少能让伤害翻倍,而攻击牌顶多能让伤害翻倍.  

我们枚举强化牌选了几张,令 $f[i]$ 表示选 $i$ 张强化牌(其中用了 $\min\{i,K-1\}$ 张)所有方案翻的倍数之和,令 $g[i]$ 表示选 $i$ 张攻击牌(其中用了 $\max\{K-M+i,1\}$ 张)所有方案产生的攻击之和.那么最终答案就是 $\sum_{i=0}^M f[i]g[M-i]$.  

先对强化牌**从大到小**排序,第 $i$ 张记为 $w_1[i]$. 对于 $f[i]$ , 我们暂时把它看成 $f[i,j]$ ,也就是多一维表示当前处理到第 $i$ 张强化牌.  
如果选的强化牌不到 $K-1$ 张时,第 $i$ 张选了就得用,或者干脆不选, $f[i,j]=f[i-1,j-1]\times w_1[i]+f[i-1,j]$.  
如果选的强化牌不少于 $K-1$ 张时,第 $i$ 张选了也用不得,或者不选也没关系, $f[i,j]=f[i-1,j-1]+f[i-1,j]$.  
虽然没有必要,但是滚动数组可以滚到一维.  

对攻击牌**从小到大**排序,第 $i$ 张记为 $w_2[i]$. 对于 $g[i]$ , 我们也暂时看成 $g[i,j]$.  
如果选的攻击牌不多于 $M-K+1$ 张时,选的强化牌张数不少于 $K-1$ 张,也就是说攻击牌只能选 $1$ 张,用组合数计算一下第$w_2[i]$产生的贡献(显然,如果选了 $i$ 之后的攻击牌, $w_2[i]$ 就没有贡献了,所以只能在前 $i-1$ 里面选剩下的 $j-1$ 张牌),$g[i,j]=g[i-1,j]+w_2[i]\times\binom{i-1}{j-1}$.  
如果选的攻击牌多于 $M-K+1$ 张时,选的强化牌张数小于 $K-1$ 张,转移时除了 $w_i$ 产生的贡献以外(计算方法同上),还要加上之前 $i-1$ 张攻击牌产生的贡献,即 $g[i-1,j-1]$.  
这也可以滚动数组.

上面这个转移是怎么做到用的攻击牌数满足要求的呢?很明显当 $j\in[1,M-K+1]$ 时保证了只用一张.根据下面那个转移可以发现,对于 $j>M-K+1$ ,都是用了 $j-(M-K)$ 张,而此时强化牌用了 $M-j$ 张,加起来就是 $K$ 啦.


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
#define getchar() ( p1 == p2 && ( p1 = bf, p2 = bf + fread( bf, 1, 1 << 21, stdin ), p1 == p2 ) ? EOF : *p1++ )
char bf[1 << 21], *p1(bf), *p2(bf);
template<typename T>
inline void read( T &x ){ char t(getchar()), flg(0); x = 0;
    for ( ; !isdigit(t); t = getchar() ) flg = t == '-';
    for ( ; isdigit(t); t = getchar() ) x = x * 10 + ( t & 15 );
    flg ? x = -x : x;
}

const int _ = 1515, mod = 998244353;
int T, N, M, K, w1[_], w2[_], C[_][_];
int f[_], g[_];
inline int dec( int x ){ return x >= mod ? x - mod : x; }
inline bool cmp( int x, int y ){ return x > y; }

signed main(){
    read(T), C[0][0] = 1;
    fp( i, 1, 1500 ){
        C[i][0] = 1; fp( j, 1, 1500 ) C[i][j] = dec(C[i - 1][j - 1] + C[i - 1][j]);
    }
    while( T-- ){
        read(N), read(M), read(K);
        fp( i, 1, N ) read(w1[i]); fp( i, 1, N ) read(w2[i]);
        sort(w1 + 1, w1 + N + 1, cmp), sort(w2 + 1, w2 + N + 1);
        memset( f, 0, sizeof f ), memset( g, 0, sizeof g ), f[0] = 1;
        fp( i, 1, N ) fd( j, min(i, M - 1), 1 )
            f[j] = j < K ? (f[j] + (i64)f[j - 1] * w1[i]) % mod : dec(f[j] + f[j - 1]);
        fp( i, 1, N ) fd( j, min(i, M), 1 )
            g[j] = (g[j] + (i64)w2[i] * C[i - 1][j - 1] + (j > M - K + 1 ? g[j - 1] : 0)) % mod;
        int ans(0); fp( i, max(0, M - N), min(N, M) ) ans = (ans + (i64)f[i] * g[M - i]) % mod;
        printf( "%d\n", ans );
    }
    return 0;
}
```

