---
title: spoj26368 PWRANDMOD - Power and Mod 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:17:14
comments: true
tags:
 - 快速幂
 - spoj
keywords:
description: spoj26368
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/75.jpg
mathjax: true
---

# LINK

[spoj PWRANDMOD](https://www.spoj.com/problems/PWRANDMOD/)
[luogu remote judge SP26368](https://www.luogu.org/problemnew/show/SP26368)

# 思路

很沙比的快速幂...
但是数据范围十分坑，刚好long long存不下...  
咋办？`__int128`！  
但是`__int128`不能直接用cin或scanf读入，要自己手写(习惯写快读的童鞋就没关系了)  
还有，不知道怎么回事__int128也WA了,于是还写了个龟速乘,然后就过了???  
个人认为不写龟速乘也不会爆.(可能写炸了吧?)  
下面就是代码辣  
该讲的都放在注释里

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define LL __int128

LL T, a, b, m, ans;
char bf[1 << 25], *p;

LL read(){//快读
	LL x(0);
	while( !isdigit(*p) ) ++p;
	while( isdigit(*p) ) x = ( x * 10 ) + ( *p ^ '0' ), ++p;
	return x;
}

void write( LL x, bool flg = 1 ){//快写(懒得用fwrite
	if ( x == 0 ){
		if ( flg ) putchar('0');
		return;
	}
	write( x / 10, 0 );
	putchar( x % 10 + '0' );
}


LL Mul( LL x, LL y ){ LL ans(0); for ( x %= m; y; x = ( x + x ) % m, y >>= 1 ) if ( y & 1 ) ans = ( ans + x ) % m; return ans; }
//龟速乘
LL Pow( LL x, LL y ){ LL ans(1); for ( x %= m; y; x = Mul( x, x ), y >>= 1 ) if ( y & 1 ) ans = Mul( ans, x ); return ans; }
//快速幂
//我这里用位运算方法实现，当然如果你喜欢二分也完全没有问题

int main(){
	bf[fread( bf, 1, 1 << 25, stdin )] = '\0'; p = bf;
	T = read();
	while( T-- ){
		a = read(); b = read(); m = read(); ans = 1;//别忘了初值
		write(Pow( a, b )); putchar('\n');//调用函数即可
	}
    
    return 0;
}
```