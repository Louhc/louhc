---
title: spoj25784 BUBBLESORT - Bubble Sort 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:21:02
comments: true
tags:
 - 树状数组
 - spoj
keywords:
description: spoj25784
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/92.jpg
mathjax: true
---

# LINK

[spoj BUBBLESORT](https://www.spoj.com/problems/BUBBLESORT/)
[luogu remote judge SP25784](https://www.luogu.com.cn/problem/SP25784)

# 思路

题目虽然是冒泡排序，但不可能模拟吧?$O(N^2\times T)$的算法是很难卡过去的.  
冒泡排序的基本原理就是交换两个元素。我们发现，每交换一次，就可以消去一个逆序对.  
所以,我们可以得出结论:冒泡排序的交换次数就是该序列的逆序对数.(这个结论在逆序对题中十分常见)
因此,运用归并排序,求出逆序对即可.(当然也可以用树状数组)具体看代码注释.

# 代码
```cpp
#include<bits/stdc++.h>
using namespace std;
#define mod 10000007
#define MAXN 10005

int T, N, ans;
int a[MAXN], b[MAXN];

void Merge_sort( int l, int r ){
	if ( l == r ) return;//只有一个元素不用排序
	int mid((l + r) >> 1);
	Merge_sort( l, mid );//左边排序
	Merge_sort( mid + 1, r );//右边排序
	int i1(l), i2(mid + 1), c(l);//把左右两边归并
	while( i1 <= mid && i2 <= r ){
		if ( a[i1] <= a[i2] ) b[c++] = a[i1++];//必须写<=
		else b[c++] = a[i2++], ans = ( ans + mid - i1 + 1 ) % mod;//顺便统计逆序对个数（右边元素之前有mid-i1+1个比它大的）
	}
	while( i1 <= mid ) b[c++] = a[i1++];//剩余的元素排到后面
	while( i2 <= r ) b[c++] = a[i2++];
	for ( int i = l; i <= r; ++i ) a[i] = b[i], b[i] = 0;//复制回原数组
}

int main(){
	scanf( "%d", &T );
	for ( int Ti = 1; Ti <= T; ++Ti ){
		scanf( "%d", &N );
		for ( int i = 1; i <= N; ++i ) scanf( "%d", &a[i] );
		ans = 0;//初始化很重要
		Merge_sort( 1, N );
		printf( "Case %d: %d\n", Ti, ans );
	}
	return 0;
}

```