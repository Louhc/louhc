---
title: uva1328 Period 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:38:07
comments: true
tags:
 - KMP
 - uva
keywords:
description: uva1328
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/105.jpg
mathjax: true
---

# LINK

[UVA1328](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=446&page=show_problem&problem=4074)
[luogu remote judge UVA1328](https://www.luogu.com.cn/problem/UVA1328)
[poj1961](http://poj.org/problem?id=1961)

# 写在前面

> Substr(i, j)表示s的子串s[i\~j]
>
> 这里s的下标从**1**开始
>
> i的上一个匹配：一个位置j,满足Substr(1, j) == Substr(i - j + 1,N)
>
> 下面黑线表示字符串，其中红框中包含的字符相等（这是自然，同一个字符串嘛）。
>
> ![](https://img2018.cnblogs.com/blog/1431616/201812/1431616-20181218102734586-2122926692.png)
>
> j还要满足
>
> ![](https://img2018.cnblogs.com/blog/1431616/201812/1431616-20181218103344286-2025081922.png)
>
> （注意啦 两条黑线表示同一个字符串，只是位置不同）
>
> （其实这也算是KMP的复习吧。。。）
>
> 下面图中红框中都表示相同

# 思路

你看看下面代码,真的很简单,关键就是如何推出这个结论.
(我不用next,用了f做数组名,希望大家不要看不习惯,意思是一样的)
![](https://img2018.cnblogs.com/blog/1431616/201812/1431616-20181218104051370-846710528.png)
粉色部分也表示相同。这很明显，因为字符是一一对应的嘛(同一个字符串位置相同、长度相同的字符串当然一样)。
由于红框内完全相同，还可以——
![](https://img2018.cnblogs.com/blog/1431616/201812/1431616-20181218104300780-1011214916.png)
继续对应!灰线表示在原字符串中是对应的.
还可以对应!
![](https://img2018.cnblogs.com/blog/1431616/201812/1431616-20181218104619297-427336946.png)
**可能**就会出现这样的情况!(当然可能最前面不够长度)
因此，只要f[i]>0，i前面肯定有循环节!(只不过不知道是否完整,bb|abb|abb|abb这样也看作是)而且循环节长度为i - f[i] + 1.因此只要判断循环节长度能否将长度整除即可.具体请见代码(真的短).

# 代码

```cpp
#include<cstdio>
using namespace std;
#define MAXN 1000005

int N, T;
char s[MAXN];
int f[MAXN];

int main(){
	while( ~scanf( "%d", &N ) && N ){
		scanf( "%s", s + 1 ); T++;
		printf( "Test case #%d\n", T );
		int t(0); f[1] = 0;
		for ( int i = 2; i <= N; ++i ){
			while ( s[i] != s[t + 1] && t ) t = f[t];
			if ( s[i] == s[t + 1] ) t++;
			f[i] = t;
			if ( f[i] != 0 && i % ( i -  f[i] ) == 0 ) printf( "%d %d\n", i, i / ( i - f[i] ) );
		}
		putchar('\n');
	}
	return 0;
}
```