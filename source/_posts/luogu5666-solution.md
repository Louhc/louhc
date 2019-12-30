---
title: 洛谷P5666 树的重心 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-12-17 13:08:45
comments: true
tags: 
 - 树的重心
 - 树上倍增
 - 洛谷
keywords: 
description: 洛谷P5666
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/109.jpg
mathjax: true
---

# LINK

[洛谷P5666](https://www.luogu.com.cn/problem/P5666)

# 写在前面

重儿子:这与树链剖分中的重儿子意义相同.对于有根树的节点 $x$,其最大的子树(即节点数最多)的根节点.

先注意一下,这里 $\frac n2$ 并不表示整除,就是普通的除法,但是有一些(不如说很大部分)与整除是等价的.请读者自行判断.

* 如果 $x$ 节点是一棵树的重心,那么删去 $x$ 以及与其相连的边,剩下最大的连通块大小不超过 $\frac n2$.
* 上面那个命题的反命题仍然成立.也就是说如果 $x$ 是一棵树的一个节点,删去 $x$ 以及与其相连的边,剩下连通块大小不超过 $\frac n2$,那么 $x$ 就是这棵树的重心.**这是本题算法中判断一个节点是否为重心的方式.**
* 如果 $x$ 是一棵有根树的根,并且有一棵子树大小不小于 $\frac n2$,那么树的重心一定在这棵子树内.
* 根据上面这条命题,我们可以得到找重心的一种方式:从根开始,沿着重儿子不断往下找,重心肯定能被访问到.并且如果有两个重心,肯定是连续的两个.

因为上面这些命题要么是基本的图论芝士,要么只需要稍微推导一下,这里就不加以证明了.

# 思路

考虑断开一条边 $(u,v)$.

分别以 $u,v$ 为根执行*写在前面*中的找重心方法.

如果暴力做的话复杂度还是超级高.~~不如普通的暴力.~~

每次只走一步太慢了,我们用倍增优化.

$f[u][i]$ 表示从 $u$ 开始沿着重儿子走 $2^i$ 步.走到第一个可能是重心的点 $x$(也就是第一个满足 $x$ 重儿子所在子树大小不超过 $\frac n2$ 的).很容易发现, $x$ 以及 $x$ 的重儿子是唯二能成为重心的节点.判断一下就 OK.

$f$ 数组可以最开始预处理以 $1$ 为根的情况,然后换根就可以了.

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define i64 long long
#define rgt register
#define fp( i, b, e ) for ( int i(b), I(e); i <= I; ++i )
#define fd( i, b, e ) for ( int i(b), I(e); i >= I; --i )
#define go( i, b ) for ( int i(b), v(to[i]); i; v = to[i = nxt[i]] )
template<typename T> inline bool cmax( T &x, T y ){ return x < y ? x = y, 1 : 0; }
template<typename T> inline bool cmin( T &x, T y ){ return y < x ? x = y, 1 : 0; }
#define getchar() ( p1 == p2 && ( p1 = bf, p2 = bf + fread( bf, 1, 1 << 21, stdin ), p1 == p2 ) ? EOF : *p1++ )
char bf[1 << 21], *p1(bf), *p2(bf);
template<typename T>
inline void read( T &x ){ char t(getchar()), flg(0); x = 0;
	for ( ; !isdigit(t); t = getchar() ) flg = t == '-';
	for ( ; isdigit(t); t = getchar() ) x = x * 10 + ( t & 15 );
	flg ? x = -x : x;
}

const int _ = 300055;
int T, N;
int hd[_], nxt[_<<1], to[_<<1], tot;
int sz[_], f[_][20], sn1[_], sn2[_];
int _sz[_];//换根后子树的大小

inline void addedge( int x, int y ){
	nxt[++tot] = hd[x], hd[x] = tot, to[tot] = y,
	nxt[++tot] = hd[y], hd[y] = tot, to[tot] = x;
}

void DFS1( int u, int fa ){
	sz[u] = 1, sn1[u] = sn2[u] = 0;
	go( i, hd[u] ) if ( fa ^ v ){
		DFS1(v, u), sz[u] += sz[v];
		if ( sz[v] > sz[sn1[u]] ) sn2[u] = sn1[u], sn1[u] = v;
		else if ( sz[v] > sz[sn2[u]] ) sn2[u] = v;
	} f[u][0] = sn1[u]; fp( i, 1, 18 ) f[u][i] = f[f[u][i - 1]][i - 1];
}

i64 ans;
inline int get( int x, int n ){ return max(_sz[f[x][0]], n - _sz[x]) <= (n >> 1) ? x : 0; }

void DFS2( int u, int fa ){
	go( j, hd[u] ) if ( v != fa ){
		_sz[u] = N - _sz[v];
		if ( _sz[fa] > _sz[sn1[u]] || (sn1[u] == v && _sz[fa] > _sz[sn2[u]]) ) f[u][0] = fa;
		else if ( sn1[u] == v ) f[u][0] = sn2[u]; else f[u][0] = sn1[u];
		fp( i, 1, 18 ) f[u][i] = f[f[u][i - 1]][i - 1];
		int n, t;
		n = _sz[v] >> 1, t = v;
		fd( i, 18, 0 ) if ( _sz[f[f[t][i]][0]] > n ) t = f[t][i];
		if ( _sz[f[t][0]] > n ) t = f[t][0];
		ans += get(t, _sz[v]) + get(f[t][0], _sz[v]);
		n = _sz[u] >> 1, t = u;
		fd( i, 18, 0 ) if ( _sz[f[f[t][i]][0]] > n ) t = f[t][i];
		if ( _sz[f[t][0]] > n ) t = f[t][0];
		ans += get(t, _sz[u]) + get(f[t][0], _sz[u]);
		DFS2(v, u);
	}
	_sz[u] = sz[u], f[u][0] = sn1[u]; // 改回去 
	fp( i, 1, 18 ) f[u][i] = f[f[u][i - 1]][i - 1];
}

signed main(){
	read(T); int x, y;
	while( T-- ){
		ans = 0, memset( hd, 0, sizeof hd ), tot = 1;
		read(N); fp( i, 2, N ) read(x), read(y), addedge(x, y);
		DFS1(1, 0), memcpy( _sz, sz, sizeof sz ), DFS2(1, 0);
		printf( "%lld\n", ans );
	}
	return 0;
}
```

