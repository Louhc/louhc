---
title: hdu3823 Prime Friend 解题报告
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 题解
date: 2019-09-13 07:53:01
comments: true
tags:
 - 质数,质数筛法
 - hdu
keywords: 
description: hdu3823
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/68.jpg
mathjax: true
---

# LINK

[hdu3823](http://acm.hdu.edu.cn/showproblem.php?pid=3823)

# 思路

先线性筛出所有质数，然后对所有邻质数对进行处理。为了方便描述，我们设一对邻质数的差为Ki，很明显，Ki > 150时是毫无用处的(因为输入中两个数最极端的差为150)，并且，对于每个小于等于150的Ki，只用保留较大质数小于等于150的所有邻质数与大于150的第一对邻质数。
有点拗口 ~~(语文没学好T-T)~~ 也就是说，**Ki大于150的，除了第一个，其他都不要了**，因为它们肯定不及第一个优(求的是x的最小值嘛)，但是留下的都有价值 。

# 代码

```cpp
#include<cstdio>
#include<vector>
using namespace std;
#define si 13626407
//最大的有效质数是这个,输出max(a[i][j])得到的(节约空间，人人有责)

int T;
int A, B, sum;//sum为 已取完所有 有效素数 的Ki有几个
bool p[si + 5];//加5免得越界
int v[887319], tot; // 1~si范围内质数的个数+5  tot存储目前素数个数
bool c[155];//判断第一个大于150的并且差为Ki的有没有出现
vector<int> a[155];//a[Ki]存储所有差为Ki有效的邻质数(较小的那个)

inline void init(){//线性筛素数
    p[1] = 1;//1不是素数
    int t;
    for ( int i = 2; i <= si; ++i ){
        if ( !p[i] ){
            v[++tot] = i;
            if ( i <= 150 ){//开始时没加，但也可以过，数据比较水
                a[0].push_back(i);
            }else{
                if ( !c[0] ){
                    c[0] = 1;
                    a[0].push_back(i);
                }
            }
            if ( i != 2 ){
                if ( i <= 150 ) a[i - t].push_back(t);
                else{
                    if ( i - t <= 150 && !c[i - t] ){
                        c[i - t] = 1;
                        a[i - t].push_back(t);
                        sum++;
                    }
                }
            }
            t = i;
        }
        if ( sum >= 75 ) break; // Ki只可能是偶数(因为质数除2外都是奇数，奇数减奇数为偶数) 因此只有 150 个(当然了，除2、3外，但是这里2和3不计入sum)
        for ( int j = 1; j <= tot; ++j )
            if ( i * v[j] <= si ) p[i * v[j]] = 1;
            else break;
    }
}

int main(){
    init();
    scanf( "%d", &T );
    for ( int I = 1; I <= T; ++I ){
        scanf( "%d%d", &A, &B );
        if ( A > B ){
            int t(A); A = B; B = t;//如果A > B 就交换 使 A <= B
        }
        int ans(-1);
        for ( int i = 0; i < a[B - A].size(); ++i ){//在所有差为B - A的质数对中找第一个满足条件的数
            if ( A <= a[B - A][i] ){
                ans = a[B - A][i] - A;
                break;
            }
        }
        printf( "Case %d: %d\n", I, ans );
    }
    return 0;
}
```