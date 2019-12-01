---
title: 洛谷P1337 [JSOI2004]平衡点 / 吊打XXX 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:47:52
comments: true
tags: 
 - 爬山算法
 - 模拟退火
 - 洛谷
keywords: 
description: 洛谷P1337
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/100.jpg
mathjax: true
---

# LINK

[洛谷P1337](https://www.luogu.com.cn/problem/P1337)

# 思路

很经典的爬山算法。

爬山算法是一个不断接近答案的~~骗分~~算法。

先设定答案的横纵坐标分别为每个点横纵坐标的平均数。

然后计算出每个点对它位置的影响（距离越远，重力越小，影响越小）；进行缩放（T）。

T不断减小时，当精度过高时就可以退出了。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 1005

int N;
int x[MAXN], y[MAXN], w[MAXN];
double ansx, ansy;
#define sqr(x) ( (x) * (x) )

inline void ClimbHill(){
	double T(1000);//设定T的初值
	while( T > 1e-5 ){
		double cx(0), cy(0), tx, ty, dis;
		for ( int i = 1; i <= N; ++i ){
			tx = x[i] - ansx; ty = y[i] - ansy;
			dis = sqrt( tx * tx + ty * ty );//计算距离
			if ( dis < 1e-10 ) continue;//避免除以0
			cx += tx * w[i] / dis;//计算对横纵坐标的影响
			cy += ty * w[i] / dis;
		}
		ansx += cx * T; ansy += cy * T; T *= 0.98;//T的值不断减小
	}
}

int main(){
	scanf( "%d", &N );
	for ( int i = 1; i <= N; ++i ) scanf( "%d%d%d", &x[i], &y[i], &w[i] ), ansx += x[i], ansy += y[i];
	ansx /= N; ansy /= N;//计算初值
	ClimbHill();
	printf( "%.3lf %.3lf\n", ansx, ansy );
	return 0;
}
```

