---
title: 洛谷P2167 [SDOI2009]Bill的挑战 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:50:49
comments: true
tags: 
 - 状压dp
 - 洛谷
keywords: 
description: 洛谷P2167
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/95.jpg
mathjax: true
---

# LINK

[洛谷P2167](https://www.luogu.org/problem/P2167)

# 思路

$f[i][j]$表示第$i$位匹配$j$(二进制表示的)字符串。
然后预处理一下每一位的每一个字符能匹配哪些字符串(还是二进制表示),下面代码中即为$mc$。
状态转移方程即为
$$f[i][j]=\sum_{k \& mc[i]['a'...'z'] = j}f[i - 1][k]$$
也许一般DP的写法并不是很好写,具体参考代码的写法好了。
滚动数组速度会变快是什么鬼)
我的代码目前大概是全谷写状压DP里面跑得最快的了,抢到rk2,rk1的巨佬看起来是写容斥的)

# 代码

```cpp
#include<cstdio>
#include<cctype>
#include<ctime>
#include<cmath>
#include<cstring>
#define u32 unsigned
#define i64 long long
#define u64 unsigned long long
#define f80 long double
#define rgt register
using namespace std;
#define mod 1000003

template<typename T>
inline void read( rgt T &x ){ x = 0; rgt char t, flg(0);
    for ( t = getchar(); !isdigit(t); t = getchar() ) flg = t == '-';
    for ( ; isdigit(t); t = getchar() ) x = x * 10 + ( t & 15 ); x = flg ? -x : x;
}

clock_t __t_bg, __t_ed;

int T, N, L, K, M;
char s[17][55];
int mc[55][30], f[2][1<<15], cr, nx, cnt[1<<15];
inline void dec( rgt int &x ){ x = x >= mod ? x - mod : x; }

signed main(){
    __t_bg = clock();
    read(T), M = 1 << 15;
    for ( rgt int i = 1; i < M; ++i ) cnt[i] = cnt[i >> 1] + ( i & 1 );
    while( T-- ){
        read(N), read(K), memset( mc, 0, sizeof mc );
        for ( rgt int i = 0; i < N; ++i )
            scanf( "%s", s[i] + 1 );
        L = strlen(s[0] + 1);
        for ( rgt int i = 0; i < N; ++i )
            for ( rgt int w = 1; w <= L; ++w )
                for ( rgt char j = 'a'; j <= 'z'; ++j )
                    if ( s[i][w] == '?' || s[i][w] == j )
                        mc[w][j & 31] |= 1 << i;
        M = 1 << N, memset( f[0], 0, M << 2 ), f[0][M - 1] = 1, cr = 0, nx = 1;
        for ( rgt int l = 0; l < L; ++l, cr = !cr, nx = !nx ){
            memset( f[nx], 0, M << 2 );
            for ( rgt int i = 0; i < M; ++i ) if ( f[cr][i] && cnt[i] >= K ){
                for ( rgt char j = 'a'; j <= 'z'; ++j )
                    dec( f[nx][i & mc[l + 1][j & 31]] += f[cr][i] );
            }
        } rgt int ans(0);
        for ( rgt int i = 0; i < M; ++i ) if ( cnt[i] == K ) dec( ans += f[cr][i] );
        printf( "%d\n", ans );
    }
    __t_ed = clock(), fprintf( stderr, "time: %.5lfs\n", double( __t_ed - __t_bg ) / CLOCKS_PER_SEC );
    return 0;
}
```