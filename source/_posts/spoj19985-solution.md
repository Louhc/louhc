---
title: spoj19985 GCDEX2 - GCD Extreme (hard) 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:12:17
comments: true
tags:
 - 杜教筛
 - 莫比乌斯反演
 - spoj
keywords:
description: spoj122
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/18.jpg
mathjax: true
---

# LINK

[spoj GCDEX2](https://www.spoj.com/problems/GCDEX2/)
[luogu remote judge SP19985](https://www.luogu.com.cn/problem/SP19985)

First of all,建议去康康SPOJ原题的数据范围,洛谷没把数据范围搬上来。  
如果你是大佬但是没有看见原题数据范围,不用往下看我瞎`**`了,直接秒了这题。

# 做这道题前你需要的
> 脑子  
电脑及其配件  
线性筛  
莫比乌斯反演  
杜教筛  
数论分块  

然后你就会发现这是很裸的杜教筛。。。。  

# 思路

(由于题目需要 这里$\varphi(1)=0$)

$$
\sum^n_{i=1}\sum^n_{j=i+1}\gcd(i,j)
=\sum^n_{i=1}\sum^{i-1}_ {j=1}\gcd(i,j)
=\sum^n_{d=1}d\sum^{n/d}_ {i=1}\sum^{i-1}_ {j=1}[\gcd(i,j)=1]
=\sum^n_{d=1}d\sum^{n/d}_ {i=1}\varphi(i)
$$

然后就可以愉快地杜教筛+数论分块辣

$\varphi * 1=ID$不用我说了吧。。

接下来是代码(C++14)——  
(SPOJ的C++似乎不能tr1,也没有C++11,所以就用C++14算了)

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define LMT 20000000
#define ULL unsigned long long
#define UI unsigned int
#define rgt register

UI T;
UI n(LMT);
UI p[LMT / 10 + 5], tot;
ULL phi[LMT + 5], N; bool vis[LMT + 5];
unordered_map<ULL, ULL> mp;
inline ULL sum( ULL x ){ return ( x & 1 ) ? ( ( x + 1 ) / 2 * x ) : ( x / 2 * ( x + 1 ) ); }
//由于2^64与2不互质,不方便用乘法逆元,直接乘再取余会爆,所以就来一波玄学操作。。。。

ULL GetSum( ULL x ){
	if ( x <= n ) return phi[x];
	if ( mp.count(x) ) return mp[x];
	rgt ULL ans(sum(x));
	for ( rgt ULL i = 2, j; i <= x; i = j + 1 )
		j = x / ( x / i ), ans -= ( j - i + 1 ) * GetSum( x / i );
	return mp.insert( make_pair( x, ans ) ), ans;
}

int main(){
	scanf( "%u", &T );
	if ( T > 200 ) n = 10000000;//避免数据小而时限小时筛完耗费太多时间
	if ( T > 1000 ) n = 10000;
	phi[1] = 1; //不要直接0了。。。否则就不是积性函数了。。。鬼知道会发生啥
	for ( rgt UI i = 2; i <= n; ++i ){
		if ( !vis[i] ) p[++tot] = i, phi[i] = i - 1;
		for ( rgt UI j = 1; j <= tot && i * p[j] <= n; ++j ){
			vis[i * p[j]] = 1;
			if ( i % p[j] ) phi[i * p[j]] = phi[i] * phi[p[j]];
			else { phi[i * p[j]] = phi[i] * p[j]; break; } 
		}
	} for ( rgt UI i = 2; i <= n; ++i ) phi[i] += phi[i - 1];
	while( T-- ){
		scanf( "%lld", &N ); ULL ans(0);
		for ( rgt ULL i = 1, j; i <= N; i = j + 1 )
			j = N / ( N / i ),//由于我们这里的phi(1)=0,前缀和要减去1
			ans += ( GetSum( N / i ) - 1 ) * ( sum(j) - sum(i - 1) );
		printf( "%llu\n", ans );
	}
	return 0;
}
```

有兴趣的童鞋可以康康https://projecteuler.net/problem=625 (除了膜数几乎一样。。。。。。。。)