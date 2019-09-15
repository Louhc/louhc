---
title: bzoj1417 Interconnect 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-12 22:16:51
comments: true
tags:
 - 期望概率
 - bzoj
 - poj
keywords: 
description: bzoj1417
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/23.jpg
mathjax: true
---

# LINK

[bzoj1417](https://www.lydsy.com/JudgeOnline/problem.php?id=1417)
[poj3156](http://poj.org/problem?id=3156)

# 思路

对于一幅图,我们只在乎连通块的状态,也就是连通块大小和数量,而连通块内部具体连接并没用什么用。
经过计算,不同的状态只有5千多种,所以可以直接用vector存储状态(由于与顺序无关,所以存的时候要先排序),然后跑DP即可。
很明显,这个状态很难按照顺序枚举,所以写成记忆化搜索的形式。
至于转移方程,也不难推。
首先,在连通块内部连边是毫无卵用的,先设总共有all种连边方式,有$ts$种情况是毫无卵用的。
先提取其他情况中的$\frac 1{all}$出来(原来是$\frac{XXXX}{all}$)
$f[v] = f[v] \times \frac{ts}{all} + \frac 1{all} \times XXXX + 1$
移项一下就可以得到
$f[v] \times \frac{all-ts}{all} = \frac 1{all} \times XXXX + 1$
再同乘$\frac{all}{all-ts}$
$f[v] = \frac 1{all-ts} \times XXXX + \frac{all}{all-ts}$
然后就可以根据每种状态的概率转移啦qwq.
最开始的状态直接用并查集瞎搞即可。
别忘了判断边界。

# 代码

```cpp
#include<bits/stdc++.h>
#define u32 unsigned
#define i64 long long
#define u64 unsigned long long
#define f80 long double
#define rgt register
using namespace std;
#define MAXN 55

clock_t __t_bg, __t_ed;

int N, M, all;
int fa[MAXN];
typedef vector<int> vc;
vc v;
map<vc, double> mp;

int find( int x ){ return fa[x] > 0 ? fa[x] = find(fa[x]) : x; }
inline void merge( int x, int y ){
    x = find(x), y = find(y);
    if ( x != y )
        fa[x] < fa[y] ? swap( x, y ), 1 : 1, fa[y] += fa[x], fa[x] = y;
}

double DP( vc c ){
    if ( mp.count(c) ) return mp[c];
    int s(c.size()), ts(0);
    if ( s == 1 ) return mp[c] = 0;
    for ( rgt int i = 0; i < s; ++i ) ts += c[i] * ( c[i] - 1 ) / 2;
    double p(1. * all / ( all - ts ));
    for ( rgt int i = 1; i < s; ++i )
        for ( rgt int j = 0; j < i; ++j ){
            vc t(c);
            t[j] += t[i], swap( t[i], t[s - 1] ), t.pop_back(),
            sort( t.begin(), t.end() );
            p += 1. * c[i] * c[j] / ( all - ts ) * DP(t);
        }
    return mp[c] = p;
}

signed main(){
    __t_bg = clock();
    while( ~scanf( "%d%d", &N, &M ) ){
        memset( fa, -1, sizeof fa ), all = N * ( N - 1 ) / 2;
        for ( rgt int i = 1, x, y; i <= M; ++i ) scanf( "%d%d", &x, &y ), merge( x, y );
        v.clear();
        for ( rgt int i = 1; i <= N; ++i ) if ( fa[i] < 0 ) v.push_back(-fa[i]);
        printf( "%.6lf\n", DP(v) );
    }
    __t_ed = clock(), fprintf( stderr, "time: %.5lfs\n", double( __t_ed - __t_bg ) / CLOCKS_PER_SEC );
    return 0;
}

```