---
title: Proofs, Arguments, and Zero-Knowledge (3)
author: louhc
categories: Reading Notes
date: 2024-07-05 00:40:02
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

## 8 MIPs and Succinct Arguments

**Multi-prover interactive proofs (MIPs)** 允许验证者 $\mathcal V$ 与多个不被信任的证明者交互，并假设证明者之间无法交流从 $\mathcal V$ 收到的 challenge。

### MIP 定义

一个关于 $\Lang\subseteq\{0,1\}^*$ 的 **k-prover interactive proof protocol** 涉及 $k+1$ 方，$1$ 个 PPT 验证者和 $k$ 个证明者。交互过程产生一个 transcript $t = (\Vrf(r),\Prv_1,...,\Prv_k)(x)$，其中 $r$ 表示 $\Vrf$ 的内部随机性。验证者最后的输出为 $\out(\Vrf, x,r,\Prv_1,...,\Prv_k)$。

1. (Completeness) There exists a tuple of prover strategies $(\Prv_1,...,\Prv_k)$ such that for every $x\in\Lang$: $$\Pr[\out(\Vrf, x,r,\Prv_1,...,\Prv_k)=\text{accept}]\geq 1−\delta_c$$
2. (Soundness) For every $x\notin\Lang$ and every tuple of prover strategies $(\Prv'_1,...,\Prv'_k)$, $$\Pr[\out(\Vrf,x,r,\Prv'_1,...,\Prv'_k) = \text{accept}]\leq\delta_s$$

如果 $\delta_c,\delta_s\leq\frac13$，称其为合法的。复杂度集 **MIP** 指的是拥有合法 k-prover IP 的语言 $\Lang$ 的集合（对于某个 $k=\poly(n)$）。

**MIP** 和 **IP** 的主要区别在于，**MIP** 是 non-adaptive 的，也就是说，在 **IP** 中证明者可以根据之前的 challenge 作出临时应对，但在 **MIP** 中就不行。从这一观点出发，我们可以证明 **MIP** 等价于 PPT oracle Turing Machine 能接受的 language。

> 设 $\Lang$ 为一个 language，$M$ 为一个 PPT oracle Turing Mahine，$M^{\mathcal O}$ 表示 $M$ 可以访问 oracle $\mathcal O$。如果对于任意 $x\in\Lang$，都存在一个 $\mathcal O$ 使得 $M^{\mathcal O}$ 以概率 $1$ 接受 $x$；对于任意 $x\notin\mathcal L$ 都存在一个 $\mathcal O$ 使得 $M^{\mathcal O}$ 至少以概率 $2/3$ 拒绝 $x$，那么就存在关于 $\mathcal L$ 的 2-prover MIP。

至于构造方法，简单地说，$\Vrf$ 可以把 $\Prv_1$ 当做 $\mathcal O$，并模拟 $M$，如果 $M$ 接受了 $x$，就从之前对 $\Prv_1$ 的所有 challenge 中随机选取一个发送给 $\Prv_2$，如果和 $\Prv_1$ 一致，那么就接受 $x$，重复这样的过程若干次。

同理可以证明 k-prover **IP**=2-prover **IP**，将原先要发给 $k$ 个 prover 的 challenge 全部发给 $\Prv_1$，然后从中随机选取一个发送给 $\Prv_2$。

### 对于 Circuit-SAT 的高效 MIP

（skip）

### 对于深层回路的简洁论证

（skip）

### 从 Circuit-SAT 到 R1CS-SAT

**中间表示（intermediate representation）** 指的是可以直接应用交互证明或论证系统的计算模型，arithmetic circuit satisfiability 就是一个例子。另一个热门好用的中间表示例子是 **rank-1 constraint system (R1CS)**。

一个 R1CS 实例指定了三个矩阵 $A,B,C\in\Fld^{m\times n}$，如果存在 $z\in\Fld^{n}$ 且 $z_1=1$ 使得 $$(A\cdot z)\circ(B\cdot z)=(C\cdot z)$$ 则称其为可满足的。我们要求 $z$ 的第一个元素 $z_1$ 是 $1$，以避免存在一个 trivial 的解，零向量。

对于每个 $i$，都存在一个被称为 rank-one constrain 的等式

$$\langle a_i,z\rangle\cdot\langle b_i,z\rangle=\langle c_i,z\rangle$$

"rank-one" 表示它只包含了一次关于两个 $z$ 中元素的线性组合的乘积。

（skip）


### MIP=NEXP

## 9 PCPs and Succinct Arguments

### PCP 定义

> A probabilistically checkable proof system (PCP) for a language $\Lang\subseteq\{0,1\}^∗$ consists of a probabilistic polynomial time verifier $\Vrf$ who is given access to an input $x$, and oracle access to a proof string $\pi\in\Sigma^{\ell}$. The PCP has completeness error $\delta_c$ and soundness error $\delta_s$ if the following two properties hold.
> 
> 1. (Completeness) For every $x\in\Lang$, there exists a proof string $\pi\in\Sigma^{\ell}$ such that $\Pr[\Vrf^{\pi}(x) = \text{accept}]\geq 1−\delta_c$.
> 2. (Soundness) For every $x\in\Lang$ and every proof string $\pi\in\Sigma^{\ell}$, $\Pr[\Vrf^{\pi}(x)=\text{accept}]\leq\delta_s$.
>
> $\ell$ is referred to as the length of the proof, and $\Sigma$ as the alphabet used for the proof. We think of all of these parameters as functions of the input size $n$. We refer to the time required to generate the honest proof string $\pi$ as the prover time of the PCP.

PCP 和 MIP 之间可以相互转换。

## 9 Interactive Oracle Proofs

### IOP 定义

>  An IOP is an IP where, in each round the verifier is not forced to read the prover’s entire message, but rather is given query access to it, meaning it can choose to look at any desired symbol of the message at the “cost” of a single query.