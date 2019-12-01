---
title: 洛谷P3133 [USACO16JAN]无线电联系Radio Contact 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-12 22:30:13
comments: true
tags:
 - 期望概率
 - 洛谷
 - bzoj
keywords: 
description: 洛谷P3133
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/81.jpg
mathjax: true
---

# LINK

[luogu3133](https://www.luogu.com.cn/problemnew/show/P3133)
[bzoj4510](https://www.lydsy.com/JudgeOnline/problem.php?id=4510)

# 思路

> ~~众所周知，DP是一个好东西，所以这题用DP。~~

这是样例的示意图 
![样例](https://img-blog.csdn.net/20180814150653860?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdWhhbmNoZW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
这道题有十分明显的阶段性,而且数据也不大,所以用DP即可. 
我们用一个二维数组`f[i][j]`表示当Farmer John到第i步,Bessie到第j步时所需花费的最小能量. 
转移方程容易推得:

```cpp
f[i][j] = min( f[i][j - 1], f[i - 1][j], f[i - 1][j - 1] ) + dist;
```

其中dist表示FJ在第i步，Bessie在j步时他们距离的平方$( tx1 - tx2 ) \times ( tx1 - tx2 ) + ( ty1 - ty2 ) \times ( ty1 - ty2 )$

这里要注意初始化:

```cpp
f[i][0] = f[i - 1][0] + dist;
f[0][i] = f[i - 1][0] + dist;
f[0][0] = 0;//因为从初始位置开始的第一步不需要能量
```

这里我用一个move函数移动坐标(用了引用变量方便一点)

# 代码

```cpp
#include<cstdio>
#include<iostream>
#include<cstring>
using namespace std;
#define MAXN 1005
#define LL long long

int N, M;
LL f[MAXN][MAXN];//开long long防止上溢
int fx, fy, bx, by, la, lb;
char a[MAXN], b[MAXN];//移动方向

void move( int &x, int &y, char d ){ //移动坐标
    switch( d ){
        case 'N': y++; break;
        case 'S': y--; break;
        case 'W': x--; break;
        case 'E': x++; break;
    }
}

LL dist( int x1, int y1, int x2, int y2 ){ //花费能量
    return ( x1 - x2 ) * ( x1 - x2 ) + ( y1 - y2 ) * ( y1 - y2 );
}

int main(){
    scanf( "%d%d", &N, &M );
    scanf( "%d%d%d%d", &fx, &fy, &bx, &by );
    scanf( "%s%s", a + 1, b + 1 );
    la = strlen( a + 1 ); lb = strlen( b + 1 );
    int tx1(fx), ty1(fy), tx2(bx), ty2(by);//FJ、Bessie的坐标
    for ( int i = 1; i <= la; ++i ){ // 初始化f[i][0](i != 0)
        move( tx1, ty1, a[i] );
        f[i][0] = f[i - 1][0] + dist( tx1, ty1, bx, by );
    }
    for ( int i = 1; i <= lb; ++i ){ //初始化f[0][i](i != 0)
        move( tx2, ty2, b[i] );
        f[0][i] = f[0][i - 1] + dist( fx, fy, tx2, ty2 );
    }
    tx1 = fx; ty1 = fy;
    for ( int i = 1; i <= la; ++i ){
        tx2 = bx; ty2 = by;
        move( tx1, ty1, a[i] );
        for ( int j = 1; j <= lb; ++j ){
            move( tx2, ty2, b[j] );
            f[i][j] = min( f[i - 1][j], f[i][j - 1] ); // 上述转移方程
            f[i][j] = min( f[i][j], f[i - 1][j - 1] );
            f[i][j] += dist( tx1, ty1, tx2, ty2 );
        }
    }
    printf( "%lld", f[la][lb] );
    return 0;
}
```

~~所以: DP是个好东西~~