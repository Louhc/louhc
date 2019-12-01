---
title: 洛谷P3931 SAC E#1 - 一道难题 Tree
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:57:36
comments: true
tags: 
 - dfs
 - 洛谷
keywords: 
description: 洛谷P3931
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/70.jpg
mathjax: true
---

# LINK

[洛谷P3931](https://www.luogu.com.cn/problemnew/show/P3931)   

# 思路

很明显，要使一个叶子节点到不了祖先，有两种选择：

> 他的某个祖先到不了根节点
>
> 它父亲->它 删了

然后我们可以遍历一遍树。

DFS( x, fa ) = $\Sigma$min(DFS( i, x ) ( 存在边x->i), val(x->i) )

fa是为了避免搜到父亲节点。

若x为叶子节点，直接返回INF

也就是说，要么断开x->i让i到不了根节点，下面就不用再删边了，要么让i到的了根节点，在下面某处再断开。

他没说c的范围，保险起见开long long

代码很短。。。真的很短。。。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define open(s) freopen( s".in", "r", stdin ), freopen( s".out", "w", stdout )
#define MAXN 100005
#define MAXM 200005
#define LL long long

int n, S;
int hd[MAXN], to[MAXM], nxt[MAXM], tot(1);
LL val[MAXM];

void Add( int x, int y, int z ){
	nxt[++tot] = hd[x]; hd[x] = tot; val[tot] = z; to[tot] = y;
	nxt[++tot] = hd[y]; hd[y] = tot; val[tot] = z; to[tot] = x;
}

LL DFS( int x, int fa ){
	LL ans(0); bool flg(0);
	for ( int i = hd[x]; i; i = nxt[i] )
		if ( to[i] != fa ) ans += min( DFS( to[i], x ), val[i] ), flg = 1;
	if ( !flg ) return LONG_LONG_MAX;
	return ans;
}

int main(){
	scanf( "%d%d", &n, &S );
	for ( int i = 1; i < n; ++i ){
		int x, y; LL z;
		scanf( "%d%d%lld", &x, &y, &z );
		Add( x, y, z );
	}
	printf( "%lld\n", DFS( S, S ) );
	return 0;
}
```