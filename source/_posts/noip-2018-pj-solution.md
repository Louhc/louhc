---
title: NOIP 2018 普及组 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 08:52:50
comments: true
tags:
 - noip
keywords: 
description: NOIP2018普及组
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/50.jpg
mathjax: true
---

# 小结

今年的题目出的有点诡异，难度跨越有点大

入门  to 普及- to 提高+/省选- to 提高+/省选-

**不过实际上没有这么难**

T3、T4 一个DP 一个暴力（虽然不是正解） 也就可以过了

**扯入正题**  

# T1 [标题统计](https://www.luogu.com.cn/problemnew/show/P5015)

这道题~~十分的水~~，没有什么技术含量，随便怎么搞都可以过。

下面是我直接放代码了。。。

```cpp
#include<bits/stdc++.h>
using namespace std;

char t;
int ans(0);

int main(){
    freopen( "title.in", "r", stdin );
    freopen( "title.out", "w", stdout );
    while( ( t = getchar() ) != EOF )
        if ( t != ' ' && t != '\n' && t != '\r' ) ans++;
    printf( "%d", ans ); 
    return 0;
}
```

# T2 [龙虎斗](https://www.luogu.com.cn/problemnew/show/P5016)

这道题没话说，只是题目长了点，好好理解一下也是不难的。
我们可以预处理出两边阵营的气势和（别忘了加上“某一刻天降神兵”）然后枚举每个兵营，把你的兵加进去，算出之后两个阵营最终的气势，然后选出气势之差绝对值最小的哪个阵营就可以了。
C++内置的函数abs由于老是忘掉是什么类型的，所以干脆手打算了。。。
话不多说，直接上代码（普及- 及以下难度的不用具体讲吧？）。**还有注意要开 long long。**（死了也别忘记）据说没开long long只能得70左右。

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long LL;

LL n, m, p1, s1, s2;
LL s[166666];
LL L, R;
LL ans(-1), q(0x7f7f7f7f7f7f7f7f);

LL Abs( LL x ){
    return x >= 0 ? x : -x;
}

int main(){
    freopen( "fight.in", "r", stdin );
    freopen( "fight.out", "w", stdout );
    scanf( "%lld", &n );
    for ( int i = 1; i <= n; ++i ) scanf( "%lld", &s[i] );
    scanf( "%lld%lld%lld%lld", &m, &p1, &s1, &s2 );
    s[p1] += s1;
    for ( int i = 1; i <= n; ++i ){
        if ( i < m ) L += ( m - i ) * s[i];
        if ( i > m ) R += ( i - m ) * s[i];
    }
    for ( int i = 1; i <= n; ++i ){
        LL tL(L), tR(R), c;
        if ( i < m ) tL += ( m - i ) * s2;
        if ( i > m ) tR += ( i - m ) * s2;
        c = Abs( tL - tR );
        if ( c < q ) ans = i, q = c;
    }
    printf( "%lld\n", ans );
    return 0;
}
```

# T3 [摆渡车](https://www.luogu.com.cn/problemnew/show/P5017) 

看这道题的时候，我（相信大家也是这样）最先想到的是贪心，但是从数据范围可以看出，如果是贪心题，数据范围不会那么小（相信NOIP不会和Luogu月赛一样，2018 11月月赛 搞个几百大小数据骗我们用DP，结果是贪心）。有些人会想(including me)，是不是在有人到达时才能发车呢？？？没想清楚就下手的话，就会浪费好多时间。仔细想想，很容易发现不一定要有人到达时发车，比如有时候，bus一回来，有个人等了2分钟，后面那个人还有INF(hh) min 才会来，如果有人到达时才能发车，那么bus将在INF min后才等到一个人，原来等了2分钟的那个人与司机等得花都谢了，所以这时候肯定是一回来就发车，虽然没有人刚好到达。

当然，我们先排序。（DP，从排序做起）。

我们用f[i]表示i min 时发一辆车，ps[i]表示1 ~ i 的人数，ts[i]表示1 ~ i 所有人开始等的时间之和。
设上一次发车是jmin时，那么j min及以前的人都已经滚粗了，我们要求j + 1 ~ i所有人等待时间之和。
等待时间之和为Σ(i - t[k]) 可以简化为 i * 人数 - Σ(t[k])， 人数、Σ(t[k])可以用前缀和来维护（即前面提到的ps、ts数组）。
然后就可以得到转移方程——

```cpp
( 0 <= i < m ) f[i] = ps[i] * i - ts[i]
( i > m ) f[i] = min{ f[j] + ( ps[i] - ps[j] ) * i - ( ts[i] - ts[j] ) }( 0 <= j <= i - m )
```

蓝鹅，这样的复杂度达到了O(MAXT ^ 2)!!!这是远远不行的。所以我们要进行优化~

### 优化I

> 对于两个人a、b( ta < tb,b = a + 1) 如果 tb - ta >= 2 * m 可以从中间断开
>
> 如果用work( l, r )表示对l、r区间范围内进行一次DP  work( ls, ta ), ls = tb;

很容易解释，因为如果两个人之间时间间隔不小于2m的话，他们是完全可以分两趟车走的。因为a最迟走的时间为ta + m - 1，车回来的时间为ta + 2m - 1,如果tb >= ta + 2m - 1，刚好可以直接粗发QAQ。(或者理解为b可以作为起点)

### 优化II

> i - 2* m + 1 <= j <= i - m

不难理解，每两趟车之间间隔不会超过或等于2m（否则中间为什么不再来一趟呢？？？）

实际上，这已经满足题目的时间复杂度要求，但是还有一个~~乱搞~~优化。(在考场上想到的)

### 优化III

> if ps[i] == ps[i - 1] 
>
> ​	j = i - m

解释这个优化要从贪心的角度考虑。

回到原来那个问题:是不是在有人到达时才能发车呢？？？

前面已经解释了答案是否定的。这从一个侧面告诉我们，如果不是在有人到达时才发车，肯定是由于车来不及回来。

所以，在最优方案中，那趟车一定会在刚好回来时发车。也就是说，i min时车刚好回来，上一次发车是在 i - m min时。

~~真是玄之又玄。~~

最终的程序不是在考场中写的，因为考场中 优化I 采用了路径压缩的方法，但是出现一些问题，被卡掉20分。

算法的复杂度也是在O(nm)级别，但常数要比Sooke大佬的代码大一些。

然后上代码！（虽然不是最好的解，但87ms也凑合吧。）

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 505
#define MAXM 205

int n, m, mm, ans;
int t[MAXN], ps[MAXM * MAXN], ts[MAXM * MAXN];
int f[MAXM * MAXN];

void work( int l, int r ){
	memset( ps, 0, sizeof ps ), memset( ts, 0, sizeof ts ), memset( f, 0, sizeof f );
	for ( int i = l + 1; i <= r; ++i ){
		t[i] -= t[l]; ps[t[i]]++; ts[t[i]] += t[i];
	}
	t[l] = 0; ps[0]++;
	for ( int i = 1; i < t[r] + m; ++i ) ps[i] += ps[i - 1], ts[i] += ts[i - 1];
	for ( int i = 1; i < t[r] + m; ++i ){
		if ( i < m ){ f[i] = ps[i] * i - ts[i]; continue; }
		if ( ps[i] == ps[i - 1] ){ f[i] = f[i - m] + ( ps[i] - ps[i - m] ) * i - ( ts[i] - ts[i - m] ); continue; }
		f[i] = INT_MAX;
		for ( int j = max( 0, i - mm + 1 ); j <= i - m; ++j ) f[i] = min( f[i], f[j] + ( ps[i] - ps[j] ) * i - ( ts[i] - ts[j] ) );
	}
	int cur(INT_MAX);
	for ( int i = t[r]; i < t[r] + m; ++i ) cur = min( cur, f[i] );
	ans += cur;	
}

int main(){
	scanf( "%d%d", &n, &m ); mm = m << 1;
	for ( int i = 1; i <= n; ++i ) scanf( "%d", &t[i] );
	sort( t + 1, t + n + 1 );
	int ls(1);
	for ( int i = 1; i < n; ++i )
		if ( t[i + 1] - t[i] >= mm ) work( ls, i ), ls = i + 1;
	work( ls, n );
	printf( "%d\n", ans );
	return 0;
}
```

~~非常抱歉，虽然这个代码通过了一些民间数据，但CCF的数据实在太强，把我原来的代码卡到了80分，以上代码70分，具体错误还不清楚，所以就暂时留坑QAQ。~~
updata 2018/11/30 23:03: 总算填好坑了QAQ



# T4 [对称二叉树](https://www.luogu.com.cn/problemnew/show/P5018)

这道题我也不知道正解是什么。我直接暴力+剪枝也跑过了所有测试点（数据太水？）

我们可以考虑当将一棵树所有节点的左右子树交换，那么搜索的顺序左变右，右变左。

即原来对于节点P，从根节点到P的路径是左右左左右，那么反转后根节点到P'的位置的路径就是右左右右左。

我们直接枚举每个子树的根节点，把原来的点、翻转后的点一一对应就可以了。

实现起来也不难。注意加点剪枝（不解释剪枝原理了）。

```cpp
#include<bits/stdc++.h>
using namespace std;

const int MAXN = 1000000 + 0xac;//AC万岁！！！

int n, v[MAXN], d[MAXN], s[MAXN];
unsigned long long M1[MAXN], M2[MAXN], M3[MAXN];
int L[MAXN], R[MAXN];

void DFS( int x, int dep ){
    s[x] = 1; M1[x] = v[x]; M2[x] = v[x]; M3[x] = dep * v[x];
    if ( L[x] != -1 ) DFS( L[x], dep + 1 ), s[x] += s[L[x]], M1[x] *= M1[L[x]], M2[x] += M2[L[x]], M3[x] += M3[L[x]];
    d[x] = dep;
    if ( R[x] != -1 ) DFS( R[x], dep + 1 ), s[x] += s[R[x]], M1[x] *= M1[R[x]], M2[x] += M2[R[x]], M3[x] += M3[R[x]];
}

bool check( int x, int y ){
    if ( v[x] != v[y] || s[x] != s[y] || M1[x] != M1[y] || M2[x] != M2[y] || M3[x] != M3[y] ) return 0;
    if ( L[x] > 0 || R[y] > 0 ){
        if ( L[x] < 0 || R[y] < 0 ) return 0;
        if ( !check( L[x], R[y] ) ) return 0;
    }
    if ( R[x] > 0 || L[y] > 0 ){
        if ( R[x] < 0 || L[y] < 0 ) return 0;
        if ( !check( R[x], L[y] ) ) return 0;
    }
    return 1;
}

bool cmp( int x, int y ){
    return s[x] > s[y];
}

int main(){
    freopen( "tree.in", "r", stdin );
    freopen( "tree.out", "w", stdout );
    scanf( "%d", &n );
    for ( int i = 1; i <= n; ++i ) scanf( "%d", &v[i] );
    for ( int i = 1; i <= n; ++i ) scanf( "%d%d", &L[i], &R[i] );
    DFS( 1, 1 );
    int ans(1);
    for ( int i = 1; i <= n; ++i )
        if ( s[i] > ans && check( i, i ) ) ans = max( ans, s[i] );
    printf( "%d\n", ans );
    return 0;
}
```

# 鸣谢

感谢叶康杰童鞋的审核。
感谢CCF提供的数据（尤其是把我T3卡掉了的那些QAQ）。