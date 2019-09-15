---
title: loj6281 数列分块入门 5 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-01-23 08:20:20
comments: true
tags: 
 - 分块
 - loj
keywords: 
description: loj6281
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.1/img/cover/17.jpg
mathjax: true
---

# LINK

[loj6281](https://loj.ac/problem/6281)

# 题意概括

区间开方,区间求和.

# 写在前面

这题需要更好地利用暴力QAQ 不暴力这题简直没法做\~  
还有注意下,由于不是同一时段的代码,所属分块之后都写成$b$而不是$p$了QAQ 希望谅解.  

# 正题

因为每个数都不超过$2^{31}-1$,所以一个数开方$5$次,肯定会为$1$.

我们可以对这一性质加以利用 好好暴力QAQ.

我们用一个数组v来记录当前块是否全为$1$,$s$记录这一块的和.当修改时,若该块全为$1$,就用不着更新了;如果不是,就暴力一次,直接把这块全开方了(别忘了更新$s$),顺便判断是否全为$1$,对$v$进行更新.对于两边不完整块,还是老样子——暴力QAQ.

# 时间复杂度分析

我们把修改完整块和不完整块分开分析.  
对于完整块,最多修改$5$次,因为前面分析过,修改$5$次肯定都为$1$了,所以上限为$O(5n)$.  
对于不完整块,每次修改最多触及$2$次,所以上限为$O(2n\sqrt n)$期间枚举完整块时间上限为$O(n\sqrt n)$.  
最后加起来就是$O(5n+3n\sqrt n)$满足复杂度要求.

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 50005

int a[MAXN], s[300], b[MAXN];
bool v[300]; 
int n, d;

void modify( int x, int y ){
	if ( b[x] == b[y] ){
		for ( int i = x; i <= y; ++i ){
			s[b[x]] -= a[i]; a[i] = sqrt(a[i]); s[b[x]] += a[i];
		}
		return;
	}
	for ( int i = x; b[i] == b[x]; ++i ){
		s[b[x]] -= a[i]; a[i] = sqrt(a[i]); s[b[x]] += a[i];
	}//修改不完整块 更新 s 的值
	for ( int i = y; b[i] == b[y]; --i ){
		s[b[y]] -= a[i]; a[i] = sqrt(a[i]); s[b[y]] += a[i];
	}
	for ( int i = b[x] + 1; i <= b[y] - 1; ++i ){
		if ( !v[i] ){//满足全为1就别做了 不满足才做
			v[i] = 1;
			for ( int j = d * ( i - 1 ) + 1; b[j] == i; ++j ){
				s[i] -= a[j]; a[j] = sqrt(a[j]); s[i] += a[j];
				if ( a[j] > 1 ) v[i] = 0;//还是不满足QAQ
			}
		}
	}
}

int Get( int x, int y ){//很好理解 不解释了
	int ans(0);
	if ( b[x] == b[y] ){
		for ( int i = x; i <= y; ++i ) ans += a[i];
		return ans;
	}
	for ( int i = x; b[i] == b[x]; ++i ) ans += a[i];
	for ( int i = y; b[i] == b[y]; --i ) ans += a[i];
	for ( int i = b[x] + 1; i <= b[y] - 1; ++i ) ans += s[i];
	return ans;
}

int main(){
	scanf( "%d", &n ); d = sqrt(n);
	for ( int i = 1; i <= n; ++i ){
		scanf( "%d", &a[i] ); b[i] = ( i - 1 ) / d + 1;
		s[b[i]] += a[i];
	}
	for ( int i = 1; i <= n; ++i ){
		int opt, l, r, c; scanf( "%d%d%d%d", &opt, &l, &r, &c );
		if ( opt ) printf( "%d\n", Get( l, r ) );
		else modify( l, r );
	}
	return 0;
}
```

显然这道题可以用线段树做,至于为什么用分块请参考标题.