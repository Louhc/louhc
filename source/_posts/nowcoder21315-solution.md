---
title: NOWCODER21315 农村连接城市 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-11-23 07:48:01
comments: true
tags:
 - 期望概率
 - 牛客网
keywords: 
description: NOWCODER21315
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/95.jpg
mathjax: true
---

# LINK

[NOWCODER21315](https://ac.nowcoder.com/acm/problem/21315)

# 思路

首先,期望是线性的.

于是我们可以求出连接每一个农村所需长度的期望,然后全部加起来就OK了.

记 $c_i$ 表示第 $i$ 个**城市**, $v_i$ 表示第 $i$ 个**农村**, $P(v_i,x)$ 表示 $v_i$ 与 $x$相连的概率, $D(x,y)$ 表示 $x$ 与 $y$ 的距离.

那么

$
ans=\sum_{i=1}^m(\sum_{j=1}^mP(v_i,v_j)D(v_i,v_j)+\sum_{j=1}^nP(v_i,c_j)D(v_i,c_j))
$

现在我们依次处理每一个农村 $v_i$ .

先考虑城市的贡献.很明显只有离最近的城市才会有贡献.

对于与它最近的城市 $c_j$ ,如果更近的农村有$x$个(不包括$v_i$),那么$v_i$与$c_j$连接的概率为$P(v_i,c_j)=\frac 1{x+1}$.

至于为什么,可以考虑所有农村的全排列(即连接的顺序),考虑其中更近的 $x$ 个农村与 $v_i$ 的相对位置,肯定是$v_i$ 在最前面,后面 $x$ 个农村怎么排就没有关系了. $v_i$ 排在第 $1$ 个,第 $2$ 个...第 $x+1$ 个的概率是相等的,因此排在最前面的概率就是 $\frac 1{x+1}$.(注意,这里说的第 $1$ 个,第 $2$ 个...仍然是这 $x+1$ 个农村的相对位置)

更近的 $x$ 个农村也有贡献.求法与上面类似,如果农村 $v_j$ 是第 $y$ 近的,那么 $P(v_i,v_j)=\frac 1{y(y+1)}$.

然后就可以愉快地求出答案了.

复杂度是 $O(n^2\log n)$ 的(也就是说数据范围搞到 $10^3$ 大概完全没问题).

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define i64 long long
#define f80 long double
#define rgt register
#define fp( i, b, e ) for ( int i(b), I(e); i <= I; ++i )
#define fd( i, b, e ) for ( int i(b), I(e); i >= I; --i )
#define go( i, b ) for ( int i(b), v(to[i]); i; v = to[i = nxt[i]] )
template<typename T> inline bool cmax( T &x, T y ){ return x < y ? x = y, 1 : 0; }
template<typename T> inline bool cmin( T &x, T y ){ return y < x ? x = y, 1 : 0; }

int N, M, n, w;
double ans;

struct node{ int x, y; }a[55], b[55];
struct D{
	double d; bool c;
	bool operator < ( const D &t )const{ return d < t.d; }
} c[105];

inline double sqr( double x ){ return x * x; }
inline double dist( node a, node b ){ return sqrt(sqr(a.x - b.x) + sqr(a.y - b.y));}

signed main(){
	cin >> N >> M;
	fp( i, 1, N ) cin >> a[i].x; fp( i, 1, N ) cin >> a[i].y;
	fp( i, 1, M ) cin >> b[i].x; fp( i, 1, M ) cin >> b[i].y;
	fp( i, 1, M ){
		n = 0;
		fp( j, 1, N ) c[++n].d = dist(a[j], b[i]), c[n].c = 1;
		fp( j, 1, M ) if ( j != i ) c[++n].d = dist(b[j], b[i]), c[n].c = 0;
		sort( c + 1, c + n + 1 );
		fp( j, 1, n ) if ( c[j].c ){ ans += c[j].d / j; break; } else ans += c[j].d / (j * (j + 1));
	} printf( "%.10lf\n", ans );
	return 0;
}
```

