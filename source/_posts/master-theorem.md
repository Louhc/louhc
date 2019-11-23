---
title: 主定理学习笔记
author: louhc
avatar: https://cdn.jsdelivr.net/gh/louhc/cdn@latest/img/custom/avatar.jpg
authorLink: louhc.github.io
authorAbout: louhc
authorDesc: louhc
categories: 学习笔记
date: 2019-10-18 20:51:09
comments: true
tags:
 - 奇技淫巧
keywords: 
description: 主定理
photos: https://cdn.jsdelivr.net/gh/louhc/cdn@1.2.1/img/cover/83.jpg
mathjax: true
---

## 主定理

### 引入

主定理可以用来求递归算法的时间复杂度.

递归算法的时间复杂度一般可以写成这鬼样:
$$
T(n)=aT(\frac{n}{b})+f(n)
$$
用主定理几乎可以**口算**算出答案.

### 内容

![](https://s2.ax1x.com/2019/10/18/KeJtzj.jpg)

实际上,说人话就是当$n^{\log_ba} \neq f(n)$时,$T(n)=\max\{O(n^{\log_ba}),O(f(n))\}$,否则的话就在第一种情况后加一个$\lg n$,即$T(n)=O(n^{\log_ba}\lg n)$.当然,这指的是能用主定理解决的情况.注意到第三种情况中还需满足的条件$af(\frac na) < cf(n)$,如果不满足的话就不能用主定理解决了.

### 例子

我们可以举几个例子来感受一下主定理的解题过程.

#### I

$$
T(n)=9T(\frac n3)+n
$$

$n^{\log_ba}=n^{\log_24}=n^2 > n$

因此是第一种情况$T(n)=n^2$.

#### II

$$
T(n)=T(\frac{2n}3)+1
$$

$n^{\log_ba}=n^{\log_{\frac32}1}=n^0=1$

因此是第二种情况$T(n)=1\times \lg n=\lg n$.

#### III

$$
T(n)=3T(\frac n4)+n\lg n
$$

$n^{\log_ba}=n^{\log_43}$

根据函数单调性可知$\log_43<1$

因此$n^{\log_43} < n < n\lg n$.

因此是第三种情况$T(n)=n\lg n$.

别忘了第三种情况还需要检验是否满足$af(\frac nb) < cf(n)$.

$af(\frac nb)=3\frac n4\lg\frac n4 < \frac 34f(n)$.

因此只要取$c=\frac 34$就可以了.

#### IV

$$
T(n)=2T(\frac n2)+n\lg n
$$

$n^{\log_ba}=n^{\log_22}=n < n\lg n$

属于第三种情况.

但是它不满足$af(\frac nb) < cf(n)$.

$2f(\frac n2)=n(\lg n-1)$

对于任何一个$c\in(0,1)$,我们都可以选取$n\geq 2^{\frac 1{1-c}}$,使得$af(\frac nb)\geq cf(n)$,因此**不能用主定理**.

暴力展开就可以了$T(n)=2T(\frac n2)+n\lg n=4T(\frac n4)+n(\lg n+(\lg n-1))=...=n\lg^2n$.

