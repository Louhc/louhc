---
title: loj6285 数列分块入门 9 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:40
comments: true
tags: 
 - 分块
 - loj
keywords: 
description: loj6285
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/30.jpg
mathjax: true
---

# [数列分块入门9](https://loj.ac/problem/6285)

# 题意概括

静态,区间求众数.

# 写在前面

还记得分块1就提到的关于块的大小有时要通过计算么??在这里就得到了体现!

# 正题

为了方便起见,我们设把数列分成$K$块\~

这道题很值得思考.

先离散化\~ 然后记录位置\~

我们可以考虑,对于L\~R之间的众数只有可能是以下三种情况：

- $b_L$块中L之后的部分
- $b_R$块中R之前的部分
- $b_L+1$块到$b_R-1$块的众数

第三条可以预处理出.

然后对于每次询问,直接枚举上述三种情况即可.

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 50005

int n, m, T;
int a[MAXN], b[MAXN], c[MAXN];
int d, f[2000][2000];
int s[MAXN];
vector<int> p[MAXN];

int Count( int l, int r, int x ){
	return upper_bound( p[x].begin(), p[x].end(), r ) - lower_bound( p[x].begin(), p[x].end(), l );
}

int Get( int l, int r ){
	if ( b[l] == b[r] ){
		int ans1(0), ans2(0);
		for ( int i = l; i <= r; ++i ){
			int t(Count( l, r, a[i] ));
			if ( t > ans2 ) ans1 = a[i], ans2 = t;
			if ( t == ans2 ) ans1 = min( ans1, a[i] );
		}
		return ans1;
	}
	int ans1(f[b[l] + 1][b[r] - 1]), ans2(Count( l, r, ans1 ));
	for ( int i = l; b[l] == b[i]; ++i ){
		int t(Count( l, r, a[i] ));
		if ( t == ans2 ) ans1 = min( ans1, a[i] );
		if ( t > ans2 ) ans1 = a[i], ans2 = t;
	}
	for ( int i = r; b[r] == b[i]; --i ){
		int t(Count( l, r, a[i] ));
		if ( t == ans2 ) ans1 = min( ans1, a[i] );
		if ( t > ans2 ) ans1 = a[i], ans2 = t;
	}
	return ans1;
}

int main(){
	scanf( "%d", &n );
	d = 0;
	while( ( 1 << d ) <= n ) d++;
	d--;
	d = (int)( n / sqrt( 2 * n * d ) );
	for ( int i = 1; i <= n; ++i ){
		scanf( "%d", &a[i] ); c[i] = a[i]; b[i] = ( i - 1 ) / d + 1;
	}
	sort( c + 1, c + n + 1 );//离散化
	m = unique( c + 1, c + n + 1 ) - c - 1;
	for ( int i = 1; i <= n; ++i ) a[i] = lower_bound( c + 1, c + m + 1, a[i] ) - c;
	for ( int i = 1; i <= n; ++i ) p[a[i]].push_back(i);//每个元素都记录位置
	
	for ( int i = 1; i <= b[n]; ++i ){
		memset( s, 0, sizeof s );
		int ans1(0), ans2(0);
		for ( int j = ( i - 1 ) * d + 1; j <= n; ++j ){
			s[a[j]]++;
			if ( s[a[j]] == ans2 ) ans1 = min( ans1, a[j] );
			if ( s[a[j]] > ans2 ) ans1 = a[j], ans2 = s[a[j]];
			if ( b[j + 1] != b[j] ) f[i][b[j]] = ans1;
		}
	}
	int x(0);
	for ( int T = 1; T <= n; ++T ){
		int l, r;
		scanf( "%d%d", &l, &r );
		int t(min( l, r )); r = max( l, r ); l = t;
		printf( "%d\n", x = c[Get( l, r )] );
	}
	return 0;
}
```

# 总结

分块大法入门1\~9就这么结束惹,但分块这个博大精深的暴力远远不止这些, 还有待大家去探索(((((ી(･◡･)ʃ)))))