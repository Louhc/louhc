---
title: 洛谷P1080 国王游戏 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:21:41
comments: true
tags: 
 - 贪心
 - 高精度
 - 洛谷
keywords: 
description: 洛谷P1080
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/61.jpg
mathjax: true
---

# LINK

[洛谷P1080](https://www.luogu.com.cn/problem/P1080)

# 思路

我们假设一个正确的序列中有两个元素 i 与 i + 1,他们左右手的数字分别为Ai，Bi，Ai+1，Bi+1

排在i大臣前面的所有人的左手上的数的乘积为T(以下向下取整省略不写)

这两位大臣得到最多的硬币为 max( T / Bi, (T * Ai) / Bi+1 ) = Ans1

假设交换这两位大臣 则他们得到最多硬币数变为 max( T / Bi+1, (T * Ai+1) / Bi ) = Ans2

要求Ans1 <= Ans2，原序列顺序可能不是最优（否则这两位大臣交换能得到更有策略）

所以要使 T / Bi 和 (T * Ai) / Bi+1都小于等于Ans2即可满足要求

由于T / Bi <= (T * Ai+1) / Bi ，T / Bi 肯定能满足要求，只要使 ( T * Ai ) / Bi+1 也小于等于 Ans2即可

(T * Ai) / Bi+1 肯定大于等于 T / Bi+1 ，所以只能使  (T * Ai+1) / Bi 大于等于(T * Ai) / Bi+1 ,否则不能达到要求

然后我们就可以列出不等式 (T * Ai) / Bi+1 <= (T * Ai+1) / Bi

因为T是正数，所以可以同时消去

Ai / Bi+1 <= Ai+1 / Bi

同乘Bi+1Bi

Ai * Bi <= Ai+1 * Bi+1

所以，只要按Ai * Bi从小到大排序就不成问题了。（当然别把国王也排序了）

注意要加高精度。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 10005

struct ppp{
    int a, b;
}p[MAXN];

int n;

bool cmp( ppp x, ppp y ){
    return x.a * x.b < y.a * y.b;
}

int s[4005], len;
int ans[4005], ans_l(0);

void Mul( int x ){
    int t(len);
    for ( int i = 1; i <= len; ++i ) s[i] *= x;
    for ( int i = 1; i <= len + 5; ++i ){
        if ( s[i] ) t = i;
        s[i + 1] += s[i] / 10;
        s[i] %= 10;
    }
    len = t;
}

void Copy( int c[], int c_l ){
    for ( int i = 1; i <= c_l; ++i ) ans[i] = c[i];
    ans_l = c_l;
}

void Out( int s[], int l ){
    for ( int i = l; i >= 1; --i ) printf( "%d", s[i] );
    putchar('\n');
}
void Div( int x ){
    int c[4005], l(-1);
    for ( int i = 1; i <= len; ++i ) c[i] = s[i];
    for ( int i = len; i >= 1; --i ){
        c[i - 1] += ( c[i] % x ) * 10;
        c[i] /= x;
        if ( c[i] && l == -1 ) l = i;
    }
    if ( ans_l == l ){
        bool flg(0);
        for ( int i = l; i >= 1; --i )
            if ( ans[i] < c[i] ){ flg = 1; break; }
            else break;
        if ( flg ) Copy( c, l );
    }
    if ( ans_l < l ) Copy( c, l );
}


int main(){
    scanf( "%d", &n );
    scanf( "%d%d", &p[0].a, &p[0].b );
    for ( int i = 1; i <= n; ++i ) scanf( "%d%d", &p[i].a, &p[i].b );
    sort( p + 1, p + n + 1, cmp );
    s[1] = 1; len = 1;
    Mul( p[0].a );
    for ( int i = 1; i <= n; ++i )
        Div( p[i].b ), Mul( p[i].a );
    Out( ans, ans_l );
    return 0;
}
```