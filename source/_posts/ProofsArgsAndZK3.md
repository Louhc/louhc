---
title: Proofs, Arguments, and Zero-Knowledge (3)
date: 2025-06-21 20:30:53
author: louhc
categories: Reading Notes
tags:
 - crypto
 - zk
 - PAZK
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

本篇笔记涵盖 [《Proofs, Arguments, and Zero-Knowledge》](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) 的**第8章** ，**第9章**和**第10章**。

<!-- more -->

## 8 多证明者交互式证明与简洁论证

**Multi-prover interactive proofs (MIPs)** 允许验证者 $\mathcal V$ 与多个不被信任的证明者交互，并假设证明者之间无法交流从 $\mathcal V$ 收到的信息。

---

### MIPs: Definition and Basic Results

**定义 8.1（MIP）** 关于语言 $\Lang\subseteq\{0,1\}^*$ 的 **k证明者交互式证明协议**（k-prover interactive proof protocol）涉及 $k+1$ 方，包括 $1$ 个 **PPT** 验证者和 $k$ 个证明者。交互过程产生一个协议交互记录（transcript） $t = (\Vrf(r),\Prv_1,...,\Prv_k)(x)$，其中 $r$ 表示 $\Vrf$ 的内部随机性。验证者最后的输出为 $\out(\Vrf, x,r,\Prv_1,...,\Prv_k)$。

1. **完备性** 存在一组证明者 $(\Prv_1,...,\Prv_k)$，使得对于任意 $x\in\Lang$，$$\Pr[\out(\Vrf, x,r,\Prv_1,...,\Prv_k)=\text{accept}]\geq 1−\delta_c$$
2. **可靠性** 对于任意 $x\notin\Lang$ 和任意一组证明者 $(\Prv'_1,...,\Prv'_k)$, $$\Pr[\out(\Vrf,x,r,\Prv'_1,...,\Prv'_k) = \text{accept}]\leq\delta_s$$

如果 $\delta_c,\delta_s\leq\frac13$，称其为有效的。复杂度集 $\mathbf{MIP}$ 指的是，对于某个 $k=\poly(n)$，拥有有效的 $k$ 证明者交互式证明协议的语言 $\Lang$ 的集合。

**MIP** 与 **IP** 的核心区别在于其**非适应性（non-adaptive）**的验证机制：

1. 在 **IP** 中，单个证明者（Prover）可以基于历史交互记录动态调整响应策略；  
2. 而在 **MIP** 中，验证者（Verifier）将多个**挑战（challenge）**分发给隔离的证明者，每个证明者仅能获取局部信息（类比“隔离审讯”）。  

这种非适应性的交互模式使得 **MIP** 的表达能力等价于**概率多项式时间谕示图灵机（Probabilistic Polynomial-Time Oracle Turing Machine）**可判定的语言类。

> 设 $\Lang$ 为一个语言，$M$ 为一个概率多项式时间谕示图灵机，其中 $M^{\mathcal O}$ 表示 $M$ 可访问谕示 $\mathcal O$。若满足：
>  
> 1. **完备性** $\forall x\in\mathcal{L}$，$\exists\mathcal{O}$ 使 $M^{\mathcal{O}}$ 以概率 $1$ 接受 $x$；
> 2. **可靠性** $\forall x\notin\mathcal{L}$，$\forall\mathcal{O}$, $M^{\mathcal{O}}$ 都以概率 $\geq2/3$ 拒绝 $x$，
> 
> 则存在关于 $\mathcal{L}$ 的双证明者交互式证明系统（2-prover MIP）。

需要注意一点，这里每次访问谕示 $\mathcal O$ 都是与之前的访问独立，也就是说 $\mathcal O$ 是“无记忆的”。因此，我们可以将其视为一个**无限证明者**交互式证明协议，$M$ 作为验证者，每次都向一个新的证明者发送一个挑战，也就是说，满足上面条件的语言的集合就是 $\mathbf{MIP}$。如果我们证明了上面的命题，也就证明了 $\mathbf{MIP}_2=\mathbf{MIP}=\mathbf{MIP}_k(\forall k\geq 2)$（第二个等号基于一个显而易见的结论 $\mathbf{MIP}_2\subseteq\mathbf{MIP}_k\subseteq\mathbf{MIP}$）。这里我用 $\mathbf{MIP}_k$ 来表示拥有 $k$ 证明者交互式证明协议的语言 $\Lang$ 的集合。

2-prover MIP 的构造方法如下：

1. $\Vrf$ 把 $\Prv_1$ 当做 $\mathcal O$，并模拟 $M^{\Prv_1}$。
2. 如果 $M^{\Prv_1}$ 接受了 $x$，就从之前对 $\Prv_1$ 的所有 challenge 中随机选取一个发送给 $\Prv_2$，如果和 $\Prv_1$ 不一致，那么就拒绝 $x$。
3. 独立地重复这样的过程若干次。

完备性显而易见，至于可靠性，由于我们要求一个没有任何前置知识的 $\Prv_2$ 作出与 $\Prv_1$ 一致的回答——这一点只有 $\Prv_1$ 的所有回答都没有使用到交互记录时才能 100% 实现，因此我们可以将每次对 $\Prv_1$ 的访问都视为独立的。

这里我想到了一个问题，那就是由于会重复多次，$\Prv_2$ 会不会根据之前重复时的交互记录，生成下一轮重复的回答呢？这样做应当是没有意义的，因为每次重复都是**独立**的，$\Prv_2$ 得到的挑战也是独立的。但是我暂时没有想到一个正式的证明。

---

### An Efficient MIP For Circuit Satisfaction

---

### A Succinct Argument for Deep Circuits

---

### Extension from Circuit-SAT to R1CS-SAT

**中间表示（intermediate representation）** 指的是可以直接应用交互证明或论证系统的计算模型，arithmetic circuit satisfiability 就是一个例子。另一个热门好用的中间表示例子是 **rank-1 constraint system (R1CS)**。

一个 R1CS 实例指定了三个矩阵 $A,B,C\in\Fld^{m\times n}$，如果存在 $z\in\Fld^{n}$ 且 $z_1=1$ 使得 $$(A\cdot z)\circ(B\cdot z)=(C\cdot z)$$ 则称其为可满足的。我们要求 $z$ 的第一个元素 $z_1$ 是 $1$，以避免存在一个平凡的解——零向量。

对于每个 $i$，都存在一个被称为 rank-one constrain 的等式

$$\langle a_i,z\rangle\cdot\langle b_i,z\rangle=\langle c_i,z\rangle$$

"rank-one" 表示它只包含了一次关于两个 $z$ 中元素的线性组合的乘积。

（to be done）

---

### MIP=NEXP

---

## 9 概率可验证证明与简洁论证

---

### PCPs: Definition and Relationship to MIPs

**定义 9.1** 关于语言 $\mathcal{L} \subseteq \{0,1\}^*$ 的**概率可验证证明系统**（Probabilistically Checkable Proof System, PCP）包含一个**概率多项式时间验证者** $\mathcal{V}$，输入 $x$ 并拥有对证明字符串 $\pi \in \Sigma^\ell$ 的谕示访问权限。

该系统为有效的，当且仅当满足：

1. **完备性**  $\forall x \in \mathcal{L}$，$\exists \pi \in \Sigma^\ell$ 使得 $\Pr\left[\mathcal{V}^\pi(x) = \text{accept}\right] \geq 1 - \delta_c$
2. **可靠性**  $\forall x \notin \mathcal{L}$ 及 $\forall \pi \in \Sigma^\ell$，满足 $\Pr\left[\mathcal{V}^\pi(x) = \text{accept}\right] \leq \delta_s$


PCP 和 MIP 紧密相关：**它们之间可以相互转换**。当然，这样的转换也会伴随着巨大的代价。

**引理 9.2** 假设语言 $\mathcal{L} \subseteq \{0,1\}^{*}$ 存在一个 $k$-证明者的 MIP，其中验证者 $\mathcal{V}$ 向每个证明者发送恰好一条消息（每条消息最多包含 $r_Q$ 比特），且每个证明者在响应中最多发送 $r_A$ 比特。那么，$\mathcal{L}$ 存在一个基于字母表 $\Sigma$（大小为 $2^{r_A}$）的 $k$-查询 PCP 系统，其证明长度为 $k \cdot 2^{r_Q}$，且具有与 MIP 相同的验证者运行时间、完备性错误和可靠性错误。

<details>
<summary>证明概要</summary>
对于 MIP 中的每个证明者 $\mathcal{P}_i$，PCP 证明会为 $\mathcal{V}$ 可能发送给 $\mathcal{P}_i$ 的每条消息预留一个条目。该条目等于证明者对该消息的响应。PCP 验证者通过模拟 MIP 验证者的行为，将 PCP 证明条目视为 MIP 中证明者的回答。
</details>

---

### Compiling a PCP Into a Succinct Argument

---

## 10 交互式预言机证明

---

### IOPs: Definition and Associated Succinct Arguments

**IOP**（Interactive Oblivious Proof） 是一种 **IP**，验证者在每一轮中无需读取证明者的**整个**消息，而是被赋予对其的**查询访问权限**，这意味着它可以选择查看消息中的任何所需符号，而“成本”仅为一次查询。

任意 IOP 都可以转化为一个非交互性论证。具体地，证明者不再发送完整的消息，而是发送一个 Merkle commitment，然后验证者发送要查询的符号的位置，证明者提供它们的值以及证明；然后，通过 Fiat-Shamir 将协议转化为非交互性的。

---

### Polynomial IOPs and Associated Succinct Arguments

在一个标准的 IOP 中，证明者发送的消息是字符串，但是在多项式 IOP 中，证明者发送的第 $i$ 条消息是函数 $h_i$，其度数不超过 $d_i$。在这章我们假设 $h_i$ 是单变量的，但是它可以是多变量的。

我们假设 $h_i$ 的参数很多，也就是说验证者无法读入所有的参数，只能对任意一点进行求值，即发送 $r$，得到 $h_i(r)$。

---

### A Polynomial IOP for R1CS-satisfiability

**事实 10.1** 设 $F$ 是一个有限域，$H$ 是 $F$ 的一个大小为 $n$ 的乘法子群。那么对于任意次数小于 $|H| = n$ 的多项式 $q$，有 $\sum_{a \in H} q(a) = q(0)\cdot|H|$。由此可得，$\sum_{a \in H} q(a) = 0$ 当且仅当 $q(0) = 0$。  

<details>
<summary>证明</summary>
由乘法群的性质可得，$$\prod_{a\in H}(X-a)=X^n-1$$

对比等式的两边，可以发现 $X^{n-1}$ 的系数 $-\sum_{a\in H}a=0$。

令 $q:X\to X^m$（$1<m<n$） 表示任意单项式。由于有限域的乘法子群是循环群，可以将 $H$ 表示为 $H=\{h,...,h^{n}\}$。因此

$$
\sum_{a\in H}q(a)=\sum_{j=1}^{n}h^{j\cdot m}
$$

而 $h^m$ 是 $h^mH$ 的生成元，且 $h^mH$ 也是乘法子群，因此 $\sum_{j=1}^{n}h^{j\cdot m}=0$。

因此，对于任意多项式 $q$，在 $H$ 上求和时，次数 $\geq1$ 的项都会被消掉，只剩下常数项，即 $q(0)。因此 $\sum_{a\in H}q(a)=nq(0)$
</details>

对于 $\mathbb F$ 的乘法子群 $H$，定义 $Z_H(X)=X^{n}-1$ 表示其零化多项式。

**引理 10.2** $\sum_{a\in H}p(a)=0$ 当且仅当存在多项式 $h^{*}(x)$ 和 $f(x)$，$\mathrm{deg}(h^{*})\leq D-n$ 且 $\mathrm{deg}(f)<n-1$，使得 $$p(X)=h^{*}(X)\cdot Z_H(X)+f(X)\cdot X$$

<details>
<summary>证明</summary>
$$
\begin{aligned}
\sum_{a\in H}p(a)=0&\Leftrightarrow p(0)=0\\
&\Leftrightarrow p(X)=h^{*}(X)\cdot Z_H(X)+f(X)\cdot X
\end{aligned}
$$
</details>

---

### FRI and Associated Polynomial Commitments