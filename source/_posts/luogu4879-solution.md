---
title: 洛谷P4879 ycz的妹子 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 10:00:32
comments: true
tags: 
 - 树状数组
 - 洛谷
keywords: 
description: 洛谷P4879
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/48.jpg
mathjax: true
---

# LINK

[洛谷P4879](https://www.luogu.org/problem/P4879)

# 思路I——树状数组+二分

我们用树状数组维护妹子个数的前缀和。

当和妹子分手时二分查找第一个妹子出现的城市即可（即Get(an) == x的第一个位置）。

其他操作$O(1)$都可以完成，这里不解释。

复杂度为$O(nlog^2n)$

~~至少比分块要快~~

这大家应该都会，代码就不给了。

# 思路II——树状数组+倍增

树状数组简直与倍增是天生一对呀~

虽然一般来说，倍增、二分的复杂度是一样的，但在这里，树状数组+倍增做到了$O(nlogn)$复杂度。

还记得树状数组是怎么求前缀和的吗？比如$1101100$

$ans=c[(1000000)_2]+c[(1100000)_2]+c[(1101000)_2]+c[(1101100)_2]$

很清楚了吧？

所以直接从高位到低位顺序枚举，如果加上这一位答案还没大于等于所求值，就加上去。最后答案$+1$即可。具体看代码。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 500005
#define LL long long

int N, M, n;
int c[MAXN];
LL ans, a[MAXN];
bool v[MAXN];

void Add( int x, int k ){ for ( ; x <= 500000; x += x & -x ) c[x] += k; }

int main(){
	scanf( "%d%d", &N, &M );
	for ( int i = 1; i <= N; ++i ) scanf( "%lld", &a[i] ), ans += a[i], Add( i, 1 ), v[i] = 1;
	char opt[5]; int x, y;
	
	for ( int i = 1; i <= M; ++i ){
		scanf( "%s", opt );
		
		if ( *opt == 'C' ){
			scanf( "%d%d", &x, &y );
			if ( v[x] ) a[x] -= y, ans -= y;
		} else if ( *opt == 'I' ){
			scanf( "%d%d", &x, &y );
			if ( !v[x] ) v[x] = 1, Add( x, 1 ), ans += y, a[x] = y;
			else ans -= a[x] - y, a[x] = y;
		} else if ( *opt == 'D' ){
			scanf( "%d", &x );
			int cur(0);
			for ( int j = 18; j >= 0; --j ) if ( ( cur | ( 1 << j ) ) <= 500000 && c[cur | ( 1 << j )] < x ) cur |= 1 << j, x -= c[cur];
			cur++; if ( v[cur] ) ans -= a[cur], a[cur] = v[cur] = 0, Add( cur, -1 );
		} else if ( *opt == 'Q' ) printf( "%lld\n", ans );
	}
	return 0;
}
```