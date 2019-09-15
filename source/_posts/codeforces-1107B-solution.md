---
title: codeforces1107B Digital root 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:55:33
comments: true
tags:
 - 打表
 - codeforces
keywords: 
description: codeforces1107B
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/94.jpg
mathjax: true
---

# LINK

[codeforces1107B](http://codeforces.com/problemset/problem/1107/B)
[luogu remote judge CF1107B](https://www.luogu.org/problem/CF1107B)

# 思路

这题看上去很不可做的样子,实际上找到规律就炒鸡简单。
先打表找规律

```
S(1)=1
S(2)=2
..
S(10)=1
S(11)=2
..
S(18)=9
S(19)=1
```

不管怎么说吧,根据从1到1000所有的数(自己写个代码验证就好了)Digital root都是从1到9不断循环的。

咋证明呢？我们先来证明一个引理：

设$F(N)$表示$N$每位数的和。$F(N)\equiv N(\mod 9)$

证明：
($\LaTeX$打的手酸)

$$\text{设N的各位数字为}a_1,a_2...a_p\text{(p表示N有几位数)} \\ \because N=a_1+a_2\times10+a_3\times10^2...+a_p\times10^{p-1}\\ \therefore N=a_1+a_2\times(9+1)+a_3\times(99+1)+..a_p\times(999..9+1)\\=a_1+a_2+a_3+...+a_p+(a_2\times9+a_3\times99+a_4\times999+...a_p\times999..9)\\ = F(N)+(a_2\times1+a_3\times11+a_4\times111+...a_p\times111..1)\\ \therefore N\equiv F(N)(\mod 9)\\QED.$$

实际上,$S(N)$就是若干$F$运算嵌套在一起。

即$S(N)=F(F(F(...F(N))))$

而根据上述结论来看$N\equiv F(N) \equiv F(F(N))\equiv F(F(F(N)))....(\mod 9)$

所以,$S(N)\equiv N(\mod 9)$

而$S(N)$不为0且为个位数,因此$S(N)=(N-1)\mod 9+1$

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define LL long long

int T;
LL k; int x;

int main(){
	scanf( "%d", &T );
	while( T-- ) scanf( "%I64d%d", &k, &x ), printf( "%I64d\n", ( k - 1 ) * 9 + x );
	return 0;
} 
```

