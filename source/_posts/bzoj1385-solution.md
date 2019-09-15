---
title: bzoj1385 [Baltic2000]Division expression 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-12 21:54:47
comments: true
tags:
 - 水题
 - bzoj
keywords: 
description: bzoj1385
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/113.jpg
mathjax: true
---

# LINK

[bzoj1385](https://www.lydsy.com/JudgeOnline/problem.php?id=1385)

# 思路

观察这个式子$E =  X_1 / X_2 / X_3 .... / X_n$.
我们设$E' = X_{a1} * X_{a2}..../ X_{b1}  / X_{b2}....$.
即把加括号后的式子改成分数线的形式,有一些元素属于分子,其他的元素属于分母.

我们发现:
- $X_1$ 不得不在分子,没有为什么,就是不可以.
- $X_2$ 不得不在分母,因为你想让它去分子它也不可能到分子.
- $X_3...X_n$ 可在分子也可在分母,总是有办法的QAQ.

如果叫你把$X_3...X_n$分一部分在分子,其他的在分母,你会怎么做？？ 当然是把全部元素放在分子呗。。。
因此最优的$E'=X_1 \times X_3 \times X_4....\times X_n / X_2$
如果真的乘起来的话肯定会溢出,所以当然要用GCD.
清爽的30行代码(～￣▽￣)～

# 代码
```cpp
#include<cstdio>
using namespace std;
#define MAXN 10005

int T;
int n, t, s;

int gcd( int x, int y ){
    return x % y == 0 ? y : gcd( y, x % y );
}

int main(){
    scanf( "%d", &T );
    while( T-- ){
        scanf( "%d", &n );
        scanf( "%d", &t );
        if ( n == 1 ){//注意特判
            printf( "YES\n" ); continue;
        }
        scanf( "%d", &s );
        s /= gcd( s, t );
        for ( int i = 3; i <= n; ++i ){
            scanf( "%d", &t );
            s /= gcd( s, t );
        }
        if ( s == 1 ) printf( "YES\n" );
        else printf( "NO\n" );
    }
    return 0;
}
```