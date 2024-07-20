---
title: Proofs, Arguments, and Zero-Knowledge (1)
author: louhc
categories: Reading Notes
date: 2024-07-03 13:46:36
tags:
 - crypto
 - mpa
 - zk
keywords: 
description:
mathjax: true
---

\newcommand{\Prv}{\mathcal{P}}
\newcommand{\Vrf}{\mathcal{V}}
\newcommand{\Lang}{\mathcal{L}}
\newcommand{\Fld}{\mathbb{F}}
\newcommand{\Pr}{\text{Pr}}
\newcommand{\out}{\text{out}}
\newcommand{\deg}{\text{deg}}
\newcommand{\poly}{\text{poly}}

# *Proofs, Arguments, and Zero-Knowledge* Reading Notes

## 3 定义和技术准备工作

### Interactive Proofs

给定一个函数 $f:\{0,1\}^n\to\mathcal R$，一个 k-message *interactive proof system (IP)* for $f$ 包含了一个**随机**（probalilistic）验证算法 $\Vrf$ （running in time poly($n$)） 和一个**确定**（deterministic）证明算法 $\Prv$。给 $\Prv$ 和 $\Vrf$ 一个共同的 $x\in\{0,1\}^n$。开始时，$\Prv$ 给出一个 $y$，声明 $f(x)=y$，然后 $\Prv$ 和 $\Vrf$ 交换一系列消息 $m_1,m_2,...,m_k$，双方交替发送消息（如果 IP 指定 $\Vrf$ 发送 $m_1$，那么 $\Prv$ 发送 $m_2$，$\Vrf$ 发送 $m_3$，以此类推）。$\Vrf$ 和 $\Prv$ 都根据之前交换的一系列消息来计算下一条消息。

整个消息序列 $t=(m_1,m_2,...,m_k)$，和 $\Prv$ 声明的 $y$ 一起，被称为 **transcript**。在协议的末尾，$\Vrf$ 需要输出 $1$ 或者 $0$，表示他是否接受 $f(x)=y$。

用 $\out(\Vrf,x,r,\Prv)$ 来表示 $\Vrf$ 的输出，其中 $r$ 表示 $\Vrf$ 的内部随机性。所以 $\out$ 是一个确定函数。

> An interactive proof system $(\Vrf,\Prv)$ is said to have completeness error $\delta_c$ and soundness error $\delta_s$ if the following two properties hold.
>
> - (Completeness) For every $x\in\{0,1\}^n$, $$\Pr_r[\out(\Vrf,x,r,\Prv)=1]\geq1-\delta_c$$
> - (Soundness) For every $x\in\{0,1\}^n$ and every deterministic prover strategy $\Prv'$, if $\Prv'$ sends a value $y \neq f(x)$ at the start of the protocol, then $$\Pr_r[\out(\Vrf,x,r,\Prv')=1]\leq \delta_s$$
>
> An interactive proof system is valid if $\delta_c,\delta_s \leq 1/3$.

除了 $\Prv$ 和 $\Vrf$ 的时间消耗，他们之间的空间消耗同样值得关注，如果他们交换了 $k$ 条消息，那么称 $\lceil\frac{k}{2}\rceil$ 为 **round complexity**。

对于任何 IP 满足 $\delta_c\leq\frac13$，都可以以一个多项式复杂度的增长为代价转换为 perfect completeness（$\delta_c=0$）。

$\delta_s\leq\frac13$ 的 $\frac13$ 只是按照习惯选取的，可以通过重复试验 $O(k)$ 次将其降为 $\delta_s^k$。

$\Vrf$ 的 randomness 是 internal 的，即不会被外界，包括 $\Prv$ 知道，这在文献中被称为 *private randomness*。如果 randomness 是 public 的，即 $\Vrf$ 每次“抛一个硬币”都能立即被 $\Prv$ 知道结果，这样的 IP 被称为 *public-coin IP*，也是非常有讨论价值的。其实它们之间的差别并不大，任何 private coin IP 都可以通过 public-coin IP 来模拟（以一个多项式的复杂度增长为代价）。

使用确定性证明算法 $\Prv$ 只是为了方便，将其改成随机性并不会影响 IP 的定义。

**Interactive Proofs for Languages Versus Functions** 对于 languages 的 IP 和上面的定义有所不同，

- Completeness. For any $x\in\Lang$, there is some prover strategy that will cause the verifier to accept with high probability.
- Soundness. For any $x\notin\Lang$, then for every prover strategy, the verifier rejects with high probability.

主要的区别在于，对于 $x\notin\Lang$，$\Prv$ 不需要证明 $f_{\Lang}(x)=0$。

但是 for functions 和 for languages 可以联系起来，一个关于 $f$ 的 IP 可以改写为关于 $\Lang=\{(x,y)\,|\,y=f(x)\}$ 的 IP。

### Argument Systems

> An argument system for a function $f$ is an interactive proof for $f$ in which the soundness condition is only required to hold against prover strategies that run in polynomial time.

也就是说无限算力的攻击者可能可以做“伪证”。之前的 soundness 被称为 *statistical soundness* 或 *information-theoretic soundness*。Argument Systems 有时被称为 *computationally sound proofs*。

### The Power of Interaction

**IP** 指的是一族可以被一个 interactive proof system with a polynomial time verifier 解决的 language。**NP** 可以视为被限制不能交互、不能使用随机的 **IP**。事实上，**IP** 是等价与 **PSPACE**（多项式空间可解决，可能使用指数时间），人们相信它是远大于 **NP** 的。

interactive proof 的 power 来源于随机性和可交互的结合。如果不使用随机（或者等价地，要求 $\delta_s=0$），那么交互就没有意义（因为 $\Prv$ 可以预测 $\Vrf$ 的输出）。另一方面，如果不使用交互，那么得到的是 **MA**（Merlin-Arthur），一般认为它是等价于 **NP** 的。

### Schwartz-Zippel Lemma

> Lemma 3.3 (Schwartz-Zippel Lemma). Let $\Fld$ be any field, and let $g$ : $\Fld^m\to\Fld$ be a nonzero $m$-variate polynomial of total degree at most $d$. Then on any finite set $S\subseteq \Fld$,
> $$\Pr_{x\leftarrow S^m}[g(x)=0]\leq\frac{d}{|S|}$$

### Low Degree and Multilinear Extensions

（先skip，补完 chapter 2 再补这个）

---

## 4 IP 实例

这章主要是一些 classical cases，后面一大部分就先 skip 了（

### The Sum-Check Protocol

给定一个 $v$ 元多项式 $g$，The Sum-Check Protocol 要求证明者提供

$$H:=\sum_{b_1\in\{0,1\}}\sum_{b_2\in\{0,1\}}\cdots\sum_{b_v\in\{0,1\}}g(b_1,...,b_v)$$

协议过程如下

> - At the start of the protocol, the prover sends a value $C_1$ claimed to equal the value $H$ defined in Equation (4.1).
> - In the first round, $\Prv$ sends the univariate polynomial $g_1(X_1)$ claimed to equal 
> $$\sum_{(x_2,...,x_v)\in\{0,1\}^{v−1}}g(X_1, x_2,...,x_v)$$.
> $\Vrf$ checks that 
> $$C_1 = g_1(0) +g_1(1)$$
> , and that $g_1$ is a univariate polynomial of degree at most $\deg_1(g)$, rejecting if not. Here, $\deg_j(g)$ denotes the degree of $g(X_1,...,X_v)$ in variable $X_j$.
> - $\Vrf$ chooses a random element $r_1\in \Fld$, and sends $r_1$ to $\Prv$.
> - In the $j$th round, for $1 < j < v$, $\Prv$ sends to $\Vrf$ a univariate polynomial $g_j(X_j)$ claimed to equal
> $$\sum_{(x_{j+1},...,x_v)\in\{0,1\}^{v−j}}g(r_1,...,r_{j−1},X_j,x_{j+1},...,x_v)$$.
> $\Vrf$ checks that $g_j$ is a univariate polynomial of degree at most $\deg_j(g)$, and that $g_{j−1}(r_{j−1})=g_j(0)+g_j(1)$, rejecting if not.
> $\Vrf$ chooses a random element $r_j\in\Fld$, and sends $r_j$ to $\Prv$.
> - In Round $v$, $\Prv$ sends to $Vrf$ a univariate polynomial $g_v(X_v)$ claimed to equal $g(r_1,...,r_{v−1},X_v)$. $\Vrf$ checks that $g_v$ is a univariate polynomial of degree at most $\deg_v(g)$, rejecting if not, and also checks that $$g_{v−1}(r_{v−1}) = g_v(0)+g_v(1)$$
> - $\Vrf$ chooses a random element $r_v\in\Fld$ and evaluates $g(r_1,...,r_v)$ with a single oracle query to $g$. $\Vrf$ checks that $g_v(r_v)=g(r_1,...,r_v)$, rejecting if not.
> - If $\Vrf$ has not yet rejected, $\Vrf$ halts and accepts.

之后的暂时先 skip。

### #SAT Problem

一个在变量 $x_1,x_2,...,x_n$ 上的**布尔公式（Boolean Formula）**，指的是一棵二叉树，它的每个叶节点标记着一个随机变量 $x_i$ 或者它的非 $\lnot x_i$，每个非叶节点表示它两个儿子的 AND 或者 OR。树的每个结点也叫作**门（gate）**。布尔公式的大小 $S$ 表示叶节点的数量。

**布尔电路（Boolean Circuit）** 和布尔公式几乎一样，但是布尔电路每个门的输出端可以有多条连边，也就是说，一个门的输出值可以被多个下游门使用。

**The #SAT Problem** 对于在 $n$ 个变量上的大小为 $S=\poly(n)$ 的布尔公式 $\phi$，求

$$\sum_{x\in\{0,1\}^n}\phi(x)$$

#SAT 被认为是一个相当困难的问题，甚至求解是否存在 $\phi(x)=1$ 都需要指数时间，但是存在 Verifier 为多项式时间的 IP 解决 #SAT。

将 $\phi$ 转换为算数电路（arithmetic circuit），将 AND 转换位 $x\cdot y$，将 OR 转换为 $x+y-x\cdot y$，然后就可以使用 sum-check protocol 了，此时 soundness error 为 $S/|\Fld|$，可以令 $|\Fld|=S^4$。

通过 #SAT Problem 可以证明 **IP** = **PSPACE**。（skip）