---
title: 洛谷P2397 yyy loves Maths VI (mode) 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 09:51:42
comments: true
tags: 
 - 奇技淫巧
 - 洛谷
keywords: 
description: 洛谷P2397
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/0.jpg
mathjax: true
---

# LINK

[洛谷P2397](https://www.luogu.org/problem/P2397)

# 思路

题目意思很简单，就是求众数。然鹅，，，，，空间贼小，，，，  
这里的突破口就是`这个众数出现次数超过了一半`，所以我们可以运用一种神奇的方法。
我们不断将两个不同的数字删去，最后剩下的数肯定相同的，它就是众数。
为什么呢？假设最坏的情况——都是众数与其它数一起删去，但是`这个众数出现次数超过了一半`，所以众数不可能全部被消去，剩下的数就是众数。如果非众数与非众数相消去，众数剩下的会更多，会更优。
这好像叫**摩尔投票法**。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;

int n, a, b, t;

int main(){
	scanf( "%d", &n );
	scanf( "%d", &a ); b = 1;
	for ( int i = 2; i <= n; ++i ){
		scanf( "%d", &t );
		if ( t == a ) b++;
		else if ( b ) b--;
		else a = t, b = 1;
	}
	printf( "%d\n", a );
	return 0;
}
```

---

# 拓展

摩尔投票算法可以拓展到 出现次数超过1/3,甚至1/k的情况。  
只要把一次消掉2个数改成消掉k个数就可以了。