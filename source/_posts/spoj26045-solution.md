---
title: spoj26045 GCDMAT2 - GCD OF MATRIX (hard) 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:08:38
comments: true
tags:
 - 莫比乌斯反演
 - spoj
keywords:
description: spoj26045
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/30.jpg
mathjax: true
---

# LINK

[spoj GCDMAT2](https://www.spoj.com/problems/GCDMAT2/)
[luogu remote judge SP26045](https://www.luogu.com.cn/problem/SP26045)

这大概是我做过的最毒瘤的卡常题了。。。

# 膜拜区

膜一发Min_25跑得最快 orz
膜一发liouzhou_101跑得第二快 orz
膜一发@Judge_Cheung 洛谷此题一血 orz

# 思路

为了方便叙述,先进行以下处理:
```cpp
--a1,--b1
```
如果你来做这道题,说明你已经把GCDMAT简单版切了。
你会得到这样的柿子——

$$s(x,y)=\sum_{i=1}^x \sum_{j=1}^{y}\gcd(i,j)=\sum_{i=1}^{\min(x,y)}\varphi(i)\times\lfloor x/i\rfloor\times\lfloor y/i \rfloor$$

$$ans=s(a2,b2)+s(a1,b1)-s(a2,b1)-s(a1,b2)$$

这是$O(T\sqrt n)$的算法,但是常数呢？
首先做一次$s$有4倍常数。
然后调用4次就是4倍常数。
这16倍常数谁顶得住啊,还进行那么多次除法运算,常数当然得高到爆炸。
我们先来进行最基础的优化,优化掉一半的常数。

$$n=\min(a2, b2)$$

$$ans=\sum_{i=1}^{n}\varphi(i)\times(\lfloor a2/i \rfloor-\lfloor a1/i \rfloor)\times(\lfloor b2/i \rfloor-\lfloor b1/i \rfloor)$$

算一算可以发现,这样常数已经减少到8倍常数了。
但是仍然高到爆炸。/ll
先放个代码——

```cpp
inline int dec( rgt int x ){ return x >= mod ? x - mod : x; }
inline int rec( rgt int x ){ return x < 0 ? x + mod : x; }
inline int get( int n, int x ){ return ( n / x ) ? n / ( n / x ) : INT_MAX; }
 
int main(){
    rgt int i, j;
    for ( scanf( "%d%d%d", &T, &N, &M ), N = max( N, M ), phi[1] = 1, i = 2; i <= N; ++i ){
        !phi[i] ? p[++tot] = i, phi[i] = i - 1 : 0;
        for ( j = 1; j <= tot && i * p[j] <= N; ++j ){
            phi[i * p[j]] = i % p[j] ? phi[i] * phi[p[j]] : phi[i] * p[j];
            if ( i % p[j] == 0 ) break;
        }
    }
    for ( rgt int i = 2; i <= N; ++i ) phi[i] = dec( phi[i - 1] + phi[i] );
    rgt int a1, b1, a2, b2, n, ans(0);
    while( T-- ) for ( ans = 0, scanf( "%d%d%d%d", &a1, &b1, &a2, &b2 ), --a1, --b1, n = max( a2, b2 ), i = 1; i > n ? printf( "%d\n", ans ), 0 : 1; i = j + 1 )
        j = min( min( get( a1, i ), get( b1, i ) ), min( get( a2, i ), get( b2, i ) ) ),
        ans = dec( ans + rec( phi[j] - phi[i - 1] ) * (LL)rec( a2 / i - a1 / i ) % mod * (LL)rec( b2 / i - b1 / i ) % mod );
    return 0;
}
```

这份代码的卡常程度还是十分naive的,极限数据大概要跑`10s~20s`左右,根本不能卡进时限。

咋办？

SPOJ上跑的最快的两位神仙——`Min_25`与`liouzhou_101`

我在推特问了`Min_25`,Judge用QQ问了`liouzhou_101`,得到两种神奇的卡常方式(都是优化除法的)——

1. `liouzhou_101`每次先预处理出所有除法,归并排序、去重,可以减少很多次除法运算。
2. `Min_25`最开始预处理出`1~N`的倒数,然后把除法改成乘法。
![](https://cdn.luogu.com.cn/upload/pic/56377.png)
然后再尽量减小运算的次数就OK了。(为了卡常数码风变得十分鬼畜,我都认不出我的代码了/xk)

<script src="https://ideone.com/e.js/muZUDQ" type="text/javascript" ></script>

# 代码

```cpp
#pragma GCC optimize("Ofast")

#include<cstdio>
#include<cmath>
#include<cctype>

#define mod 1000000007
#define LL long long
#define rgt register

//常数能咋省就咋省，代码不能看不是问题，常数小就行

char bf[1500000],*p;inline void read(rgt int&x){for (x=0;!isdigit(*p);++p);for (;isdigit(*p);++p)x=x*10+(*p&15);}
//减小一点快读的常数
//感觉快写没必要

int T, N, M, pr[100005], tot, t;
LL s[1000005], phi[1000005];
double invs[1000005];
inline int min( rgt int x, rgt int y ){ return x > y ? y : x; }//手写min(位运算似乎还不如三元运算符
#define div(x,y) (int)(x*invs[y])//改成乘法优化除法

signed main(){
    bf[fread(bf,1,1500000,stdin)]='\0',p=bf; rgt int i, j;
    for ( invs[1] = 1, read(T), read(N), read(M), N = N > M ? N : M, phi[1] = 1, i = 2; i <= N; ++i ){
        !phi[i] ? pr[++tot] = i, phi[i] = i - 1 : 0, invs[i] = 1. / i * ( 1 + 1e-15 );
        for ( j = 1; j <= tot && i * pr[j] <= N; ++j ) phi[i * pr[j]] = i % pr[j] ? phi[i] * phi[pr[j]] : phi[i] * pr[j], ( i % pr[j] == 0 ) ? j = 2000000000 : 0;
    } rgt int n, m, a1, b1, a2, b2, at1, ta1, at2, ta2, bt1, tb1, bt2, tb2; rgt LL ans(0), tmp, *pt;
    for ( i = 1, tmp = 0, pt = s; i <= N; ++i ) tmp = *++pt = tmp + phi[i];
    while( T-- ){ ans = at1 = ta1 = at2 = ta2 = bt1 = tb1 = bt2 = tb2 = 0, read(a1), read(b1), read(a2), read(b2), --a1, --b1, n = a2 < b2 ? a2 : b2, m = sqrt(n);
        for ( i = 1, pt = phi; i <= m; ++i ) ans += *++pt *(div(a2,i)-div(a1,i))*(div(b2,i)-div(b1,i));//前sqrt(n)直接循环,因为数论分块得到的区间长度也都是1,直接算可以减少0.3~0.4的常数
        for ( i = m + 1, tmp = s[m]; i <= n;
            at1 < i ? ta1 = div(a1,i), at1 = ta1 ? div(a1,ta1) : 2000000000, 0 : 0,//没必要重新算的千万不要重新算
            at2 < i ? ta2 = div(a2,i), at2 = ta2 ? div(a2,ta2) : 2000000000, 0 : 0,
            bt1 < i ? tb1 = div(b1,i), bt1 = tb1 ? div(b1,tb1) : 2000000000, 0 : 0,
            bt2 < i ? tb2 = div(b2,i), bt2 = tb2 ? div(b2,tb2) : 2000000000, 0 : 0,
            j = min(min(at1,at2),min(bt1,bt2)), ans+=(s[j]-tmp)*(ta2-ta1)*(tb2-tb1),
        tmp=s[j], i = j + 1 ); printf( "%lld\n", ans % mod );//最后取模减小常数
    }
    return 0;
}
```
写完这道题,你就能体会到将常数从20s卡到2.5s内的乐趣。
但是我还是卡不到`Min_25`和`liouzhou_101`那么快 /ll

# Update on 2019.4.13

发现一种更神奇的卡常方式,再加上原先的代码,可以跑得比Min_25还快!
直接说出来没意思对不对.... 来意会一下
~~免得那么多人来抢rk1~~
这是一份检验某个性质是否成立的代码。

```cpp
#include<bits/stdc++.h>
using namespace std;

int N = 1000000;

int main(){
//  freopen( "out", "w", stdout );
    for ( int n = 1, m; n <= N; ++n ){
        m = sqrt(n) + 1;
        for ( int i = m, j, cur(n / i); i <= n; i = j + 1, --cur ){
            if ( n / i != cur ) return printf( "Wrong on %d!\n", n ), 0;
            j = n / ( n / i );
        }
//      printf( "%d is OK\n", n );
    }
    return 0;
}
```
如果发现了什么,您就可以跑到1.5s左右,甚至可以直接抢到rk1

~~然后不要说出来,然别人意会去吧qwq~~

**がんばれ!**

# 结尾彩蛋

最后放上目前我最快的代码

```cpp
#pragma GCC optimize("Ofast")

#include<cstdio>
#include<cmath>
#include<cctype>
#include<ctime>

#define mod 1000000007
#define LL long long
#define rgt register
#define div(x,y) (int)(x*invs[y])
double invs[1000005];

char bf[1500000],*p;inline void read(rgt int&x){for (x=0;!isdigit(*p);++p);for (;isdigit(*p);++p)x=x*10+(*p&15);}
char ot[600000],*pp=ot; char stk[100], tp;
inline void write( rgt int x ){ tp = 0; rgt int t;
    while( t=div(x,10), stk[++tp]=x-10*t, x=t );
    while( *pp = stk[tp] | 48, ++pp, --tp );
    *pp = '\n', ++pp;
}

int T, N, M, pr[100005], tot, t;
LL s[1000005], phi[1000005];
inline int min( rgt int x, rgt int y ){ return x > y ? y : x; }

int bg, ed;

signed main(){
//  freopen( "data", "r", stdin );
//  freopen( "out", "w", stdout );
    bg = clock();
    bf[fread(bf,1,1500000,stdin)]='\0',p=bf; rgt int i, j;
    for ( invs[1] = 1, read(T), read(N), read(M), N = N > M ? N : M, phi[1] = 1, i = 2; i <= N; ++i ){
        !phi[i] ? pr[++tot] = i, phi[i] = i - 1 : 0, invs[i] = 1. / i * ( 1 + 1e-15 );
        for ( j = 1; j <= tot && i * pr[j] <= N; ++j ) phi[i * pr[j]] = i % pr[j] ? phi[i] * phi[pr[j]] : phi[i] * pr[j], ( i % pr[j] == 0 ) ? j = 2000000000 : 0;
    } rgt int n, m, a1, b1, a2, b2, at1, ta1, at2, ta2, bt1, tb1, bt2, tb2; rgt LL ans(0), tmp, *pt;
    for ( i = 1, tmp = 0, pt = s; i <= N; ++i ) tmp = *++pt = tmp + phi[i];
    while( T-- ){
        ans = at1 = ta1 = at2 = ta2 = bt1 = tb1 = bt2 = tb2 = 0,
        read(a1), read(b1), read(a2), read(b2), --a1, --b1,
        n = a2 < b2 ? a2 : b2, m = sqrt(a2 > b2 ? a2 : b2);
        
        for ( i = 1, pt = phi; i <= m; ++i ) ans += *++pt *(div(a2,i)-div(a1,i))*(div(b2,i)-div(b1,i));
        
        ta1 = div(a1,m), at1 = ta1 ? div(a1,ta1) : 2000000000,
        ta2 = div(a2,m), at2 = ta2 ? div(a2,ta2) : 2000000000,
        tb1 = div(b1,m), bt1 = tb1 ? div(b1,tb1) : 2000000000,
        tb2 = div(b2,m), bt2 = tb2 ? div(b2,tb2) : 2000000000;
        
        for ( i = m + 1, tmp = s[m]; i <= n;
            at1 < i ? --ta1, at1 = ta1 ? div(a1,ta1) : 2000000000 : 0,
            at2 < i ? --ta2, at2 = ta2 ? div(a2,ta2) : 2000000000 : 0,
            bt1 < i ? --tb1, bt1 = tb1 ? div(b1,tb1) : 2000000000 : 0,
            bt2 < i ? --tb2, bt2 = tb2 ? div(b2,tb2) : 2000000000 : 0,
            j = min(min(at1,at2),min(bt1,bt2)), ans+=(s[j]-tmp)*(ta2-ta1)*(tb2-tb1),
        tmp=s[j], i = j + 1 ); write( ans % mod );
    }
    ed = clock();
    fprintf( stderr, "%d ms", ed - bg );
    return fwrite(ot,1,pp-ot,stdout),0;
}
```