---
title: loj6277 数列分块入门 1 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:00
comments: true
tags: 
 - 分块
 - loj
keywords: 
description: loj6277
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/6.jpg
mathjax: true
---

# LINK

[loj6277](https://loj.ac/problem/6277)

# 题意概括

区间加法,单点求值.

# 写在前面

数列分块是个好东西...我这里详细介绍一下分块算法,便于初学者的理解(我这个蒟蒻原来也是看不懂分块).

# 分块简要介绍

先把数组分成几个块块,然后就可以对它们整体操作啦.

也就是说,把一个长度为的数组,拆分成一个个长度为$\sqrt n$小块(当然,最后一块可能不完整,但是不用管),记录每个数所属的块;也就是这样.(方便起见,我们直接再开一个数组来记录所属分块,虽然本题中可以临时计算,但在有些题目中这一步显得尤为重要)

```cpp
scanf( "%d", &n ), m = (int)sqrt(n);
for ( int i = 1; i <= n; ++i ) p[i] = ( i - 1 ) / m + 1;
for ( int i = 1; i <= n; ++i ) scanf( "%d", &a[i] );
```

然后,就可以瞎暴力辣！

所有的分块都是这样.把一个数列分成几块,然后对它们进行批量处理.一般来说,我们直接把块大小设为$\sqrt n$,但实际上,有时候我们要根据数据范围、具体复杂度来确定.

# 正题

当有修改时,对于完整的块,直接维护一个数组$v$记录整个块加过的数(每块共同的加数),不完整的就直接暴力在原数组$a$上直接加.询问时,直接输出原数组的值+所属块的共同加数即可.

# 代码

```cpp
#include<cstdio>
#include<cmath>
using namespace std;
#define MAXN 50005

int n, a[MAXN], p[MAXN], m, v[300];
int opt, l, r, c;

void Add( int l, int r, int c ){
	if ( p[l] == p[r] ){//同属一分块时直接暴力即可
		for ( int i = l; i <= r; ++i ) a[i] += c;
		return;
	}
	for ( int i = l; p[i] == p[l]; ++i ) a[i] += c;//对于两边不完整(即使完整也不管,看做不完整)的分块,直接暴力即可
	for ( int i = r; p[i] == p[r]; --i ) a[i] += c;
	for ( int i = p[l] + 1; i <= p[r] - 1; ++i ) v[i] += c;//记录完整分块的共同加数
}

int main(){
	scanf( "%d", &n );
	m = (int)sqrt(n);
	for ( int i = 1; i <= n; ++i ) p[i] = ( i - 1 ) / m + 1;//记录所属分块
	for ( int i = 1; i <= n; ++i ) scanf( "%d", &a[i] );
	for ( int i = 1; i <= n; ++i ){
		scanf( "%d%d%d%d", &opt, &l, &r, &c );
		if ( opt == 0 ) Add( l, r, c );
		else printf( "%d\n", v[p[r]] + a[r] );//所属分块共同加数+原数组的值
	}
	return 0;
}
```

# 总结

分块代码可以比线段树简洁不少,虽然暴力但十分巧妙,而且十分灵活,适用于更多的题目.  
但是如果时间复杂度要求较高,分块的$O(n\sqrt n)$就不能承受了,所以还是要学会乖乖打线段树QAQ.