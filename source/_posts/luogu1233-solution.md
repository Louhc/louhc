---
title: 洛谷P1233 木棍加工 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:45:30
comments: true
tags: 
 - 贪心
 - 洛谷
keywords: 
description: 洛谷P1233
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/67.jpg
mathjax: true
---

# LINK

[洛谷P1233](https://www.luogu.com.cn/problem/P1233)

# 思路
一般来说，数据较大，要求顺序时，要优先考虑贪心（当然，也有可能是动态规划或其他算法，这并不绝对）。

根据题意，最终答案中1min准备后能一次处理的木棍L、W非严递减。我们可以考虑以L为关键字进行降序排序，然后在W中进行类似于“导弹拦截”这道经典题的操作，也就是用最少的装置防御最多的导弹（请自行类比 [导弹拦截](https://www.luogu.com.cn/problemnew/show/P1020) 具体原因不再赘述）。

总结一下，就是将L降序排序，然后求最长不下降子序列。

废话不多说，直接上代码。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 5005

struct ppp{
    int L, W;
}a[MAXN];

bool cmp( ppp x, ppp y ){
    return x.L > y.L;
}

int n;
int f[MAXN], tot;

void Add( int x ){
    int l(1), r(tot), mid, ans(-1);
    while( l <= r ){
        mid = ( l + r ) >> 1;
        if ( f[mid] >= x ){
            r = mid - 1; ans = mid;
        } else l = mid + 1;
    }
    if ( ans == -1 ) f[++tot] = x;
    else f[ans] = x;
}

int main(){
    scanf( "%d", &n );
    for ( int i = 1; i <= n; ++i ) scanf( "%d%d", &a[i].L, &a[i].W );
    sort( a + 1, a + n + 1, cmp );
    f[++tot] = a[1].W;
    for ( int i = 2; i <= n; ++i ) Add( a[i].W );
    printf( "%d\n", tot ); 
    return 0;
}
```
2018/11/26修改为Markdown格式