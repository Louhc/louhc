---
title: codeForces1108D Diverse Garland 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:02:45
comments: true
tags:
 - 枚举
 - codeforces
keywords: 
description: codeforces1108D
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/47.jpg
mathjax: true
---

# LINK

[codeforces1108D](http://codeforces.com/problemset/problem/1108/D)
[luogu remote judge CF1108D](https://www.luogu.org/problemnew/show/CF1108D)

# 思路

顺序枚举从2到N的所有字符,如果与之前的字符相等,就根据前后字符修改这个字符.
为什么呢?如果碰到一对相邻且相同的元素$i,i+1$，后一个元素$i+2$可能与$i+1$相等,但是$i$不可能与$i-1$相等(因为前面的元素已经过修改调整),因此,修改$i+1$可能会更优于$i$(因为如果修改$i$的话,可能还需要修改$i+1$或$i+2$).

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define Re register
#define MAXN 200005

int N, ans;
char s[MAXN];

int main(){
	scanf( "%d", &N ); scanf( "%s", s + 1 );
	for ( int i = 2; i <= N; ++i ){
		if ( s[i] == s[i - 1] ){
			ans++; char t('R');
			if ( s[i - 1] == 'R' || s[i + 1] == 'R' ){
				t = 'G';
				if ( s[i - 1] == 'G' || s[i + 1] == 'G' ) t = 'B';
			}
			s[i] = t;
		}
	}
	printf( "%d\n%s\n", ans, s + 1 );
	return 0;
}

```

