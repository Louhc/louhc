---
title: 洛谷P4343 [SHOI2015]自动刷题机 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 07:43:39
comments: true
tags:
 - 二分答案
 - 洛谷
 - bzoj
keywords: 
description: 洛谷P4343
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/25.jpg
mathjax: true
---

# LINK

[洛谷P4343](https://www.luogu.com.cn/problem/P4343)
[bzoj4590](https://www.lydsy.com/JudgeOnline/problem.php?id=4590)

# 思路

这是一道二分题. 没错这就是一道二分题.
它的单调性很明显.n越大，切的题就越少.
这里要求最小值(ans1)与最大值(ans2),所以要写2个二分,一个判断条件是$check(mid) > k$ 另一个是 $\ge$.
ans1要加1,因为$>k$求出的是 满足 $check(mid) > k$ 的 最大值。如果有答案的话，$ans1 + 1$必定是满足$check(mid) = k$最小值.
话不多说，直接上代码$(〃'▽'〃)——$

# 代码

```cpp
#include<cstdio>
#include<iostream>
using namespace std;
#define LL long long

int l, k;
LL a[100005];
LL le(1), ri, ans1, ans2, mid;

LL check( LL x ){//模拟求切题数
    LL cur(0), s(0);
    for ( int i = 1; i <= l; ++i ){
        cur = max( cur + a[i], 0ll );
        if ( cur >= x ){
            s++; cur = 0;
        }
    }
    return s;
}

int main(){
    scanf( "%d%d", &l, &k );
    for ( int i = 1; i <= l; ++i ) scanf( "%lld", &a[i] );
    le = 1; ri = 1000000000000000;//边界是个坑，走远一点就不会掉进去了嘛(｀・ω・´)反正时间复杂度不会超过O(100)
    while( le <= ri ){
        mid = ( le + ri ) >> 1;
        if ( check( mid ) > k ){
            le = mid + 1;
            ans1 = mid;
        }
        else ri = mid - 1;
    }
    ans1++;
    le = 1; ri = 1000000000000000;
    while( le <= ri ){
        mid = ( le + ri ) >> 1;
        if ( check( mid ) >= k ){
            le = mid + 1;
            ans2 = mid;
        }
        else ri = mid - 1;
    }
    if ( check( ans1 ) != k ) printf( "-1\n" );
    else printf( "%lld %lld\n", ans1, ans2 );
    return 0;
}
```