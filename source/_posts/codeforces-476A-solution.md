---
title: codeforces476A Dreamoon and Stairs 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:14:28
comments: true
tags:
 - 递推
 - codeforces
keywords: 
description: codeforces476A
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/96.jpg
mathjax: true
---

# LINK

[luogu remote judge CF476A](https://www.luogu.com.cn/problem/CF476A)
[codeforces476A](http://codeforces.com/problemset/problem/476/A)

# 思路

很明显，爬完这个台阶的最多步数是n(每次爬1层),最少步数 $\frac{n - 1}2 + 1$ (等价于 $\frac n2 + n \% 2$) (每次爬2层,如果层数是奇数，那再爬1层)，并且在( $\frac{n - 1}2 + 1$ ) ~ n 之间都可以到达。

所以只要选取( $\frac{n - 1}2 + 1$ ) ~ n 之间最小的能被m整除的数即可。

当然，这道题还可以用DP解决，那比较费时间，比较费空间，也比较难调试(谁愿意呢)，所以这里不再赘述。

# 代码

下面给出两种写法——

## 写法一

比较保险的O($\frac nm$)算法(我模拟赛时就用这种的)

```cpp
#include<cstdio>
using namespace std;

int n, m;
int ans;

int main(){
	scanf( "%d%d", &n, &m );
	while( ans < n / 2 + n % 2 ) ans += m;
	if ( ans > n ) ans = -1;
	printf( "%d\n", ans );
	return 0;
}
```

## 写法二

比较有风险的O(1)算法(就怕出错,有hack数据的请联系我)

```cpp
#include<cstdio>
using namespace std;

int n, m;
int ans;

int main(){
	scanf( "%d%d", &n, &m );
i	if ( n == 0 ) printf( "0\n" );
	ans = ( ( n / 2 + n % 2 - 1 ) / m + 1 ) * m;
	if ( ans == 0 || ans > n ) ans = -1;
	printf( "%d\n", ans );
	return 0;
}
```