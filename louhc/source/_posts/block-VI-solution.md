---
title: 「分块系列」数列分块入门6 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:25
comments: true
tags: 
 - 分块
keywords: 分块
description: 分块系列-数列分块入门6 单点插入,单点询问.
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/41.jpg
mathjax: true
---

# [数列分块入门6](https://loj.ac/problem/6282)

# 题意概括

单点插入,单点询问.

# 写在前面

分块的小技巧可多啦 O(∩\_∩)O哈哈\~

# 正题

用一个vector来记录每个块的元素.vector提供了强大的insert函数,插入某个元素只要写一点点就可以啦O(∩\_∩)O,就是一个一个枚举块,直到找到插入位置属于哪个块,然后insert一下就OK.不过,如果插入元素集中于一个块的话就尴尬了QAQ复杂度飙升.所以我们要对块进行重新分配.我采用若一个块元素多于10倍原有元素个数时就重排\~（由于数据随机分配,不进行重排其实也不会TLE,甚至直接$1$个vector插入也能过 QAQ 数据水？？？建议还是写下重排,因为完全可以卡掉不重排的）

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005

int n, d, nn;
int a[MAXN<<1];
vector<int> v[1005];

inline void mer(){//把所有元素还原到a数组中
	n = 0;
	for ( int i = 1; i <= d + 1; ++i ){
		if ( v[i].empty() ) break; 
		for ( int j = 0; j < v[i].size(); ++j ) a[++n] = v[i][j];
		v[i].clear();
	}
}

inline void div(){//把a数组元素分配到各个块中
	d = sqrt(n);
	for ( int i = 1; i <= n; ++i ) v[( i - 1 ) / d + 1].push_back(a[i]);
}

inline int Get( int wh ){
	for ( int i = 1; i <= d + 1; ++i ){
		if ( wh > v[i].size() ) wh -= v[i].size();
		else return v[i][wh - 1]; 
	}
}

inline void Ins( int wh, int x ){
	for ( int i = 1; i <= d + 1; ++i ){
		if ( wh > v[i].size() ) wh -= v[i].size();
		else{
			v[i].insert( v[i].begin() + wh - 1, x );//插入~
			if ( v[i].size() > 10 * d ) mer(), div();//重排
			return;
		}
	}
}

int main(){
	scanf( "%d", &n );
	for ( int i = 1; i <= n; ++i ) scanf( "%d", &a[i] );
	div();
	nn = n;
	for ( int i = 1; i <= nn; ++i ){
		int opt, l, r, c;
		scanf( "%d%d%d%d", &opt, &l, &r, &c );
		if ( opt ) printf( "%d\n", Get(r) );
		else Ins( l, r );
	}
	return 0;
}
```

# 总结

有些分块的技巧性很强,平时注意归纳`(*^▽^*)`