---
title: 洛谷P3911 最小公倍数之和 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:56:58
comments: true
tags: 
 - 莫比乌斯反演
 - 洛谷
keywords: 
description: 洛谷P3911
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/60.jpg
mathjax: true
---

# LINK

[洛谷P3911](https://www.luogu.org/problem/P3911)

# 思路

我们先用c数组预处理出每个数出现的次数,那么原问题变为求解
$$\sum_{i=1}^n\sum_{j=1}^n lcm(i,j)\times c_i \times c_j$$
其中n表示最大的数。

然后？莫比乌斯反演来一发

为了避免麻烦,这里先使$T=kd$

$$
\begin{aligned}
& \sum_{i=1}^n\sum_{j=1}^n lcm(i,j)\times c_i \times c_j\\
=& \sum_{i=1}^n\sum_{j=1}^n \frac{i \times j\times c_i \times c_j}{\gcd(i,j)} \\ 
=& \sum_{d=1}^n\sum_{i=1}^{\lfloor n/d \rfloor}\sum_{j=1}^{\lfloor n/d \rfloor}[\gcd(i,j)=1]d\times i \times j \times c_{id} \times c_{jd} \\
=& \sum_{d=1}^n\sum_{i=1}^{\lfloor n/d \rfloor}\sum_{j=1}^{\lfloor n/d \rfloor}\sum_{k|\gcd(i,j)}\mu(k) \times d\times i \times j \times c_{id} \times c_{jd} \\
=& \sum_{d=1}^n\sum_{k=1}^{\lfloor n/d\rfloor}\sum_{i=1}^{\lfloor n/kd\rfloor}\sum_{j=1}^{\lfloor n/kd\rfloor}\mu(k)\times d \times i \times j \times k^2 \times c_{idk} \times c_{jdk} \\
=& \sum_{T=1}^{n}T\times(\sum_{i=1}^{\lfloor n/T \rfloor}i\times c_{iT})^2\sum_{k|T}\mu(k)\times k
\end{aligned}
$$

看起来十分可怕,其实都是莫比乌斯反演题的基本操作。

然后就可以开心地预处理出$\sum_{k|T}\mu(k)\times k$,暴力求解啦！

这样的复杂度为$O(\sum^n_{i=1}\frac ni)$,即$O(n \ln n)$,可以轻松过此题。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define LL long long
#define MAXN 50005
#define rgt register

int N, M, cnt[MAXN], mu[MAXN], p[MAXN], tot, v[MAXN];
LL s[MAXN];
LL ans(0);

int main(){
	scanf( "%d", &N ); for ( rgt int i = 1, x; i <= N; ++i ) scanf( "%d", &x ), ++cnt[x], M = max( M, x );
	N = M, mu[1] = 1;
	for ( rgt int i = 2; i <= N; ++i ){//线性筛出mu
		if ( !v[i] ) p[++tot] = i, mu[i] = -1;
		for ( rgt int j = 1; j <= tot && i * p[j] <= N; ++j ){
			v[i * p[j]] = 1;
			if ( i % p[j] == 0 ){ mu[i * p[j]] = 0; break; }
			else mu[i * p[j]] = -mu[i];
		}
	}
	
	for ( rgt int i = 1; i <= N; ++i )
		for ( rgt int j = i; j <= N; j += i )
			s[j] += 1ll * mu[i] * i;//预处理提到过的那玩意
	
	for ( rgt int T = 1; T <= N; ++T ){
		rgt LL cur(0);
		for ( rgt int i = 1, I = N / T; i <= I; ++i ) cur += 1ll * cnt[i * T] * i;//暴力求解
		ans += T * cur * cur * s[T];
	} printf( "%lld\n", ans );
	return 0;
}
```