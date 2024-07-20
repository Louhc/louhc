---
title: Introduction to Modern Cryptography(9) 数论基础
author: louhc
categories: Reading Notes
date: 2024/6/29 16:01:22
tags:
 - crypto
keywords: 
description:
mathjax: true
---

\usepackage{amsmath,amssymb,amsthm}

\newcommand{\Enc}{\text{Enc}}
\newcommand{\Dec}{\text{Dec}}
\newcommand{\Gen}{\text{Gen}}
\newcommand{\Vrfy}{\text{Vrfy}}
\newcommand{\Mac}{\text{Mac}}
\newcommand{\K}{\mathcal{K}}
\newcommand{\M}{\mathcal{M}}
\newcommand{\C}{\mathcal{C}}
\newcommand{\Z}{\mathbb{Z}}
\newcommand{\Adv}{\mathcal{A}}
\newcommand{\Qry}{\mathcal{Q}}
\newcommand{\Hsh}{\mathcal{H}}
\newcommand{\Pr}{\text{Pr}}
\newcommand{\negl}{\text{negl}}
\newcommand{\poly}{\text{poly}}
\newcommand{\Func}{\text{Func}}
\newcommand{\Perm}{\text{Perm}}
\newcommand{\PrivK}{\text{PrivK}}

# *Introduction to Modern Cryptography* Reading Notes

## 9 Number Theory and Cryptographic Hardness Assumptions

关于群论的部分全部跳过。

### 离散对数问题和 CDH/DDH

用 $\mathcal G$ 表示一个通用的、多项式时间的群生成算法，输入 $1^n$，输出一个阶为 $q$（其中 $||q||=n$） 的循环群 $\mathbb G$ 的**描述（description）**和它的一个生成元 $g$。它将 $\mathbb G$ 的每个元素都表示为一个比特串，并且要求存在多项式时间算法，能够验证一个任意比特串是否对应着 $\mathbb G$ 中的一个元素，能执行群运算（我的理解是，输入两个对应着 $g_1,g_2\in\mathbb G$ 的比特串，能计算 $g_1\circ g_2$ 对应的比特串）。虽然所有阶为 $q$ 的循环群都是同构的，但是不同的描述可能会导致不同的计算复杂度。

对于 $g\in\mathbb G$，存在一个唯一的 $x\in\Z_q$ 使得 $g^x=h$，称 $x$ 是 $h$ 关于 $g$ 的**离散对数（discrete logarithm）**。注意如果 $g^{x'}=h$，那么 $\log_gh=[x'\mod q]$。

我们和之前一样用测试的方式定义**离散对数问题（discrete-logarithm problem）**

> The discrete-logarithm experiment $\text{DLog}_{\Adv,\mathcal G(n)}$:
> 
> 1. Run $\mathcal G(1^n)$ to obtain $(\mathbb G, q, g)$, where $\mathbb G$ is a cyclic group of order $q$ (with $|q|=n$), and $g$ is a generator of $\mathbb G$.
> 2. Choose a uniform $h\in\mathbb G$.
> 3. $\Adv$ is given $\mathbb G, q, g, h$, and outputs $x\in\Z_q$.
> 4. The output of the experiment is defined to be $1$ if $g^x=h$, and $0$ otherwise.

根据上下文，书中的 $\mathcal G$ 是一个随机算法。如果 $\mathcal G$ 是确定性的，或者 $\mathcal G$ 的随机性被 $\Adv$ 所知，会发生什么事，我还没有想清楚。

如果对于任意的 PPT 算法 $\Adv$，都有 $\Pr[\text{DLog}_{\Adv,\mathcal G(n)}=1]\leq\negl(n)$，则称离散对数问题是难的（relative to $\mathcal G$）。而**离散对数假设**就是存在一个 $\mathcal G$ 使得离散对数问题是难的。

**The Diffie–Hellman problems** 与离散对数相关但不（被认为）等价，有两个重要的变体 **computational Diffie–Hellman (CDH) problem** 和 **decisional Diffie–Hellman (DDH) problem**。

定义函数 $\text{DH}(h_1,h_2)=g^{\log_{g}h_1\cdot\log_{g}h_2}$。

**CDH** 问题就是对于 $h_1,h_2$ 求解 $\text{DH}(h_1,h_2)$。如果离散对数问题关于 $\mathcal G$ 是难的，那么 CDH 问题关于 $\mathcal G$ 也是难的。反过来是否成立还不清楚。

**DDH** 问题就是区分 $\text{DH}(h_1,h_2)$ 和均匀随机数。如果 
$$|\Pr[\Adv(\mathbb G,q,g,g^x,g^y,g^z)]-\Pr[\Adv(\mathbb G,q,g,g^x,g^y,g^{xy})]|\leq\negl(n)$$
则称 DDH 关于 $\mathcal G$ 是难的。

CDH 难可以推出 DDH 难，但是反过来不（被认为）行。

通常选取群的时候，我们更偏好素阶群，理由先 skip。

---

### 椭圆曲线 Elliptic Curves

还不存在椭圆曲线群上解决 the discrete-logarithm problem 的亚指数算法。

椭圆曲线的定义（Weierstrass representation）：

$$E[\Z_p]\overset{\text{def}}{=}\{(x,y)\,|\,x,y\in\Z_p\text{ and }y^2=x^3+Ax+B\mod p\}\cup\{\mathcal O\}$$

其中 $\mathcal O$ 表示 point at infinity。

- $\mathcal O$ 是 $E[\Z_p]$ 的零元，$\forall P\in E[\Z_p],P+\mathcal O=\mathcal O+P=P$。
- 对于 $P_1,P_2\in E[\Z_p]$，$P_1+P_2$ 取决于 $P_1$ 和 $P_2$ 连线（如果 $P_1$ 和 $P_2$ 相等，那么就取切线）与曲线的第三个交点 $P_3=(x,y)$ 或 $\mathcal O$，若 $P_3\neq\mathcal O$，$P_1+P_2=(x,-y)$，否则 $P_1+P_2=\mathcal O$。
- $\forall P\in E[\Z_p]$，若 $P=\mathcal O$，$-P=\mathcal O$；若 $P=(x,y)$，$-P=(x,-y)$。

通过一些复杂的运算可以证明 $E[\Z_p]$ 满足加法结合律，因此是阿贝尔群。

除了 Weierstrass representation，还可以使用 **Montgomery representation**

$$
By^2=x^3+Ax^2+x\mod p
$$

需要注意的是，不是所有椭圆曲线都能写成 Montgomery representation 的形式，通过 Montgomery representation 构造的椭圆曲线群的阶都是 $4$ 的倍数。

还有 **Twisted Edwards representation**

$$ax^2+y^2=1+dx^2y^2\mod p$$

当 $a=1$ 时称为 Edwards representation。Twisted Edwards representation 和 Montgomery representation 能表示的椭圆曲线集合是一样的。

用 Twisted Edwards representation 构造椭圆曲线群时，$\mathcal O=(0,1)$。另外，如果 $a$ 是 $p$ 的二次剩余，而 $d$ 不是时，
$$P_1+P_2=(\frac{x_1y_2+x_2y_1}{1+dx_1x_2y_1y_2},\frac{y_1y_2-ax_1x_2}{1-dx_1x_2y_1y_2})$$。

**Hasse Bound** 对于质数 $p$, $p+1-2\sqrt p\leq |E[\Z_p]|\leq p+1+2\sqrt p$

并不是每个椭圆曲线群对于 the discrete-logarithm problem 都是难的，实际使用中并不会自行生成一个椭圆曲线，而是使用 NIST 等组织推荐使用的椭圆曲线群。

(skip)