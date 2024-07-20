---
title: A Pragmatic Introduction to Secure Multi-Party Computation (2) 定义
author: louhc
categories: Reading Notes
date: 2024/7/11 14:32:21
tags:
 - crypto
 - mpc
keywords: 
description:
mathjax: true
---

\usepackage{amsmath,amssymb,amsthm}

\newcommand{\Enc}{\text{Enc}}
\newcommand{\Dec}{\text{Dec}}
\newcommand{\Gen}{\text{Gen}}
\newcommand{\K}{\mathcal{K}}
\newcommand{\M}{\mathcal{M}}
\newcommand{\C}{\mathcal{C}}
\newcommand{\Adv}{\mathcal{A}}
\newcommand{\Pr}{\text{Pr}}
\newcommand{\negl}{\text{negl}}
\newcommand{\poly}{\text{poly}}
\newcommand{\Func}{\text{Func}}
\newcommand{\Perm}{\text{Perm}}
\newcommand{\PrivK}{\text{PrivK}}

# *A Pragmatic Introduction to Secure Multi-Party Computation* Reading Notes

## 2 Defining Multi-Party Computation

### Secret Sharing

> Let $D$ be the domain of secrets and $D_1$ be the domain of shares. Let $\text{Shr}:D\to D_1^n$ be a (possibly randomized) sharing algorithm, and $\text{Rec} : D^k_1\to D$ be a reconstruction algorithm. A $(t, n)$-secret sharing scheme is a pair of algorithms $(\text{Shr}, \text{Rec})$ that satisfies these two properties:
> 
> - Correctness. Let $(s_1, s_2,...,s_n) = \text{Shr}(s)$. Then, $\Pr[\forall k \geq t, \text{Rec}(s_{i_1},..., s_{i_k}) = s] = 1$.
> - Perfect Privacy. Any set of shares of size less than $t$ does not reveal anything about the secret in the information theoretic sense. More formally, for any two secrets $a, b\in D$ and any possible vector of shares $v=v_1, v_2, ..., v_k$ , such that $k < t$,$\Pr[v = \text{Shr}(a)|_k ] = \Pr[v = \text{Shr}(b)|_k ]$, where $|_k$ denotes appropriate projection on a subspace of $k$ elements.

### Real-Ideal Paradigm

在 **Ideal World** 中，每一方都和一个**可信**方 $\mathcal T$ 进行交流，他们将自己的输入 $x_i$ 发送给 $\mathcal T$，$\mathcal T$ 计算出 $\mathcal F(x_1,...,x_n)$ 并将结果返回给每一个人。

在 **Real World** 中，每一方都有一个 $\pi_i$，根据 $x_i$、一个随机带、之前接收到的信息等来决定下一步给哪一方发送什么消息，或者终止并得到最终的输出。

如果攻击者在 **Ideal World** 和 **Real World** 中能做的事一样，称该协议 $\pi$ 是安全的。

### Semi-Honest Security

> A semi-honest adversary is one who corrupts parties but follows the protocol as specified.

攻击者诚实地按照协议运行，只是从收到的消息中尽可能获取信息。（看起来类似于密钥加密中的 EAV-secure）

### Malicious Seccurity

#### Reactive functionalities

在 Ideal World 中，$\mathcal F$ 通常只是接收各方的 $x_i$ 并产生输出。在一些情形下，可能需要把 $\mathcal F$ 拓展为“交互式”的。

#### Security with abort

在一些协议中，某一方可能率先得到最终的输出，一些恶意攻击者可能会在得到输出后拒绝将消息发送给其他方。在 Ideal World 中，各方同时获取输出，这一性质被称为 output fairness，但在现实中并不是所有函数可以在满足这个性质的条件下进行多方计算。

#### Adaptive corruption

随着协议的进行，攻击者选择要入侵哪些 party。与之相对的是 static corruption。

emmm 没怎么看明白

### Hybrid Worlds and Composition

在 real world 中对 $\mathcal F$ 进行多方计算时，中间可能需要计算 $\mathcal G$。即使 $\rho$ 协议对于计算 $\mathcal G$ 是安全的，在计算 $\mathcal F$ 时直接使用 $\rho$ 协议也不一定安全。我们需要借助 universal composability (UC) framework 来构造安全的协议。

- $\text{Real}_{\pi,\Adv,Z(\kappa)}$: run an interaction involving adversary $\Adv$ and environment $Z$. When $Z$ generates an input for an honest party, the honest party runs protocol $\pi$, and gives its output to $Z$. Finally, $Z$ outputs a single bit,
which is taken as the output of $\text{Real}_{\pi,\Adv,Z(\kappa)}$.
- $\text{Ideal}_{\mathcal F,\text{Sim},Z(\kappa)}$: run an interaction involving adversary (simulator) $\text{Sim}$ and environment $Z$. When $Z$ generates an input for an honest party, the input is passed directly to functionality $\mathcal F$ and the corresponding output is given to $Z$ (on behalf of that honest party). The output bit of $Z$ is taken as the output of $\text{Ideal}_{\mathcal F,\text{Sim},Z(\kappa)}$.

> A protocol $\pi$ UC-securely realizes $\mathcal F$ if for all real-world adversaries $\Adv$ there exists a simulator $\text{Sim}$ with $\text{corrupt}(\Adv) = \text{corrupt}(\text{Sim})$ such that, for all environments $Z$:
> $$\left|\Pr[\text{Real}_{\pi,\Adv,Z(\kappa)} = 1] − \Pr[\text{Ideal}_{\mathcal F,\text{Sim},Z(\kappa)}=1]\right|$$
> is negligible (in $\kappa$).

也没咋看明白（