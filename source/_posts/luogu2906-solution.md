---
title: 洛谷P2906 [USACO08OPEN]牛的街区Cow Neighborhoods 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:54:14
comments: true
tags: 
 - 距离
 - 洛谷
keywords: 
description: 洛谷P2906
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/104.jpg
mathjax: true
---

# LINK

[洛谷P2906](https://www.luogu.com.cn/problem/P2906)

# 思路

有思考深度的好题呀！（注意下$X_i$与$x_i$表示的意义是不同的

我们观察两个点$i$、$j$的曼哈顿距离$|X_i-X_j|+|Y_i-Y_j|$。

- 如果$X_i-X_j$与$Y_i-Y_j$同号，曼哈顿距离为$|X_i+Y_i-(X_j+Y_j)|$，且$|X_i-Y_i-(X_j-Y_j)|$偏小

- 如果异号，曼哈顿距离为$|X_i-Y_i-(X_j-Y_j)|$，且$|X_i+Y_i-(X_j+Y_j)|$偏小

因此，我们发现，如果设$x_i=X_i+Y_i$，$y_i=X_i-Y_i$，$i$、$j$之间的曼哈顿距离即为$\max(|x_i-x_j|,|y_i-y_j|)$

然后用~~平衡树~~multiset一通乱搞就可以啦！

我们按照$x_i$为第一关键字，$y_i$为第二关键字排序，依次处理每个点。设当前处理的点为$i$。我们用并查集维护一个个群。

首先把所有$x$值小于$x_i-C$的点全部$erase$（删除）掉。

寻找第一个大于等于$y_i$的点$j$(lower_bound)。如果$y_j-y_i\le C$，很明显，$j$与$i$是同一个群的。

但是如果还有大于$y_i$的点$k$也满足条件呢？很明显，如果$y_k-y_i\le C$，那么$y_k-y_j\le y_k-y_i\le C$，在处理$j$或$k$的时候已经将它们合并（这取决于$x$的大小），不必再管。

然后再康康小于$y_i$的点有没有符合条件的就好啦！

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define IT multiset<pi>::iterator
#define MAXN 100005
#define LL long long
#define pi pair<LL, int>

struct node{
	int x, y;
	bool operator < ( const node &t )const{//重载运算符
		if ( x == t.x ) return y < t.y;
		return x < t.x;
	}
	void input(){
		scanf( "%d%d", &x, &y );
		x = x + y, y = x - y - y;//读入并把X、Y转换成x、y
	}
}a[MAXN];

multiset<pi> s;//因为要合并，还得记录编号（排序后的）。。所以用了个pair
IT p[MAXN];//迭代器数组，用于删除x过小的元素

int f[MAXN], sm[MAXN];//并查集、记录每个群的牛数（最后再处理

int find( int x ){ return x == f[x] ? x : ( f[x] = find(f[x]) ); }
void Merge( int x, int y ){
	x = find(x); y = find(y);
	f[x] = y;
}

int N, C, x, y;

int main(){
	scanf( "%d%d", &N, &C );
	for ( int i = 1; i <= N; ++i ) a[i].input(), f[i] = i;
	sort( a + 1, a + N + 1 );
	s.insert( make_pair( 1ll << 60, -1 ) ); s.insert( make_pair( -( 1ll << 60 ), -1 ) );//避免边界问题
	p[1] = s.insert(make_pair( a[1].y, 1 )); int tmp(1);//第一个点直接插入即可。
	
	for ( int i = 2; i <= N; ++i ){
		while( a[i].x - a[tmp].x > C ) s.erase(p[tmp++]);//把不满足要求的点删除
		
		IT t(s.lower_bound(make_pair( a[i].y, -1 )));//找第一个大于等于y的
		if ( t->first - a[i].y <= C ) Merge( i, t->second );//满足要求，合并
		t--;//找第一个小于y的
		if ( a[i].y - t->first <= C ) Merge( i, t->second );//满足要求，合并
		
		p[i] = s.insert( make_pair( a[i].y, i ) );//插入点并记录迭代器
	}
	int ans1(0), ans2(0);
	for ( int i = 1; i <= N; ++i ){
		if ( find(i) == i ) ans1++;
		ans2 = max( ans2, ++sm[find(i)] );
	}
	printf( "%d %d\n", ans1, ans2 );
	return 0;
}
```