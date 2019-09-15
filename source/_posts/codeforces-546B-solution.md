---
title: codeforces546B Soldier and Badges 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:15:19
comments: true
tags:
 - 贪心
 - codeforces
keywords: 
description: codeforces546B
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/30.jpg
mathjax: true
---

# LINK

[codeforces546B](http://codeforces.com/problemset/problem/546/B)
[luogu remote judge CF546B](https://www.luogu.org/problem/CF546B)

# 思路

其实这道题的思想很简单，就是每次遇到一个没有出现过的的数，就把之前的一个**重复**的数变成这个数。。。
我的代码可能有点奇怪。。。
我的主要思想是把取到的数的和 - 原来的和，然后就是答案。
具体看代码吧——

# 代码

```cpp
#include<cstdio>
#include<queue>
using namespace std;
#define MAXN 3005

int n, ans, t, s;
int nn;
int a[MAXN * 2];

int main(){
    scanf( "%d", &n );
    for ( int i = 1; i <= n; ++i ) scanf( "%d", &t ), a[t]++, s += t, nn = max( nn, t );//哈希计数
    t = 0;
    for ( int i = 1; i <= 6000; ++i ){
        if ( t == 0 && i > nn ) break;
        if ( t > 0 && a[i] == 0 ) t--, ans += i;//把一个该改的数改成这个数
        if ( a[i] > 1 ) t += a[i] - 1;//又多了这么多个待改变的数
        if ( a[i] ) ans += i;//不改变的话也要加哦
    }
    printf( "%d\n", ans - s );
    return 0;
}
```