---
title: bzoj1722 [Usaco2006 Mar] Milk Team Select 产奶比赛 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-12 22:24:54
comments: true
tags:
 - 背包dp
 - bzoj
keywords: 
description: bzoj1722
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/5.jpg
mathjax: true
---

# LINK

[bzoj1722](https://www.lydsy.com/JudgeOnline/problem.php?id=1722)

# 思路

**很明显**,这是一道动态规划题（看着很像背包嘛）那我们要用什么作为下标呢？

1. 以牛奶量：牛奶量的数据范围较大，最多只能开一维数组(除非你开滚动数组)。总体来说较难考虑。
2. 以关系数：关系数最多只有 N - 1 对，可以给每头牛开一个 MAXN 的数组，看起来更行得通。。。

还有，因为要考虑关系数的转换问题，所以还有开一维[0/1]表示这头牛取不取。

我们发现，这些关系是无环的，或者说这是一个森林(本来我想用拓扑排序，后来还是放弃了。。。)，所以可以再建立一头“牛祖先”，连接原来所有祖先，构成一棵树(即无母亲的节点都认0为母亲)。

对于样例，可以构成如图的一棵树：

![img](https://images2018.cnblogs.com/blog/1431616/201808/1431616-20180821115813792-530126048.jpg)

整理一下，刚才讲到建立一个 $int f[MAXN][MAXN][2];$ 的数组，$f[i][j][op]$表示以第 i 个节点为根的树 共有 j 对关系，op = 1 时表示取这个节点，op = 0 时表示不取。

可以把 关系数 作为背包容量，牛奶量 作为 价值

用分组背包的方法来考虑。

具体的部分自己慢慢理解吧。

上代码——

# 代码

```cpp
#include<cstdio>
#include<iostream>
using namespace std;
#define MAXN 505
#define INF 0x3f3f3f3f

int N, X, ans;
int c[MAXN], t;
int head[MAXN], to[MAXN << 1], nxt[MAXN << 1], tot(0);
int f[MAXN][MAXN][2];

void Add( int x, int y ){
    to[++tot] = y; nxt[tot] = head[x]; head[x] = tot; 
}

void DFS( int x, int fa ){
    for ( int i = 1; i <= N; i++ ) f[x][i][0] = f[x][i][1] = -INF;//初始值均为负无穷
    for ( int i = head[x]; i; i = nxt[i] ){
        if ( to[i] == fa ) continue;
        DFS( to[i], x );//先完成子节点
        for ( int j = N; j >= 0; --j ){//分配的关系数
            for ( int k = 0; k <= j; ++k ){//背包——
                f[x][j][1] = max( f[x][j][1], f[x][j - k][1] + max( k == 0 ? -INF : f[to[i]][k - 1][1], f[to[i]][k][0] ) );
                f[x][j][0] = max( f[x][j][0], f[x][j - k][0] + max( f[to[i]][k][1], f[to[i]][k][0] ) );
            }
        }
    }
    for ( int i = 0; i <= N; ++i ) f[x][i][1] += c[x];//若取这个节点，加上这个点的价值
}

int main(){
    scanf( "%d%d", &N, &X );
    for ( int i = 1; i <= N; ++i ){
        scanf( "%d%d", &c[i], &t );
        Add( i, t ); Add( t, i );//链式前向星存边
    }
    DFS( 0, -1 );
    int ans(N);
    while( ans >= 0 ){
        if ( f[0][ans][0] >= X ) break;
        ans--;
    }
    printf( "%d\n", ans );
    return 0;
}
```