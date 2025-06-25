---
title: Proofs, Arguments, and Zero-Knowledge (2)
date: 2025-06-20 20:30:53
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

本篇笔记涵盖 [《Proofs, Arguments, and Zero-Knowledge》](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) 的**第5章** ，**第6章**和**第7章**。

<!-- more -->

## 5 Fiat-Shamir 变换

我们之前提到过**公开随机数协议**（public-coin protocol），即验证者 $\Vrf$ 在生成一个随机数后，$\Prv$ 能立即获知这个随机数。不失一般性地，我们可以认为在公开随机数协议中，$\Vrf$ 向 $\Prv$ 发送的唯一消息就是他生成的随机数。

Fiat-Shamir 变换是一种在零知识证明领域相当重要且广泛应用的技术，可以将任意公开随机数协议 $\mathcal I$ 转换为一个非交互性的，可以公开被验证的协议 $\mathcal Q$。

---

### The Random Oracle Model

**随机函数**指的是一种特殊的函数 $R:D\to\{0,1\}^{\kappa}$，对于任意 $x\in D$，$R$ 从 $\{0,1\}^{\kappa}$ 中均匀随机选取它的值 $R(x)$。

**随机预言机模型**（Random Oracle Model，ROM）假设证明者和验证者可以访问一个黑盒随机函数 $R$，对于一次访问 $x$，如果之前从未访问过这个值，ROM 会返回 $R(x)$，即独立地从 $\{0,1\}^{\kappa}$ 随机均匀地选取一个数并返回，然后记录这个值，下次如果重复查询 $R(x)$ 时返回相同的值。

现实世界中这样的 ROM 是不存在的，一般会使用一些具体的哈希函数（如 SHA-3 等）来代替。

---

### The Fiat-Shamir Transformation

---

### Security of the Transformation

---

## 6 将程序转换为电路

在第4章中，我们提到了 GKR 协议，它是一个关于对算数电路求值的交互式证明协议。但是，现实中人们几乎不会使用算数电路来解决问题，而更偏好用一些高级编程语言如 C++，Python 等。因此，如果想在现实中大规模应用 GKR 协议，我们需要一种技术，将高级编程语言高效地转换为协议能处理的算术电路。

---

### Turning Computer Programs Into Circuit Satisfiability Instances

在**算术电路可满足性问题（Arithmetic Circuit-SAT）**中，给定电路 $\mathcal{C}$，其输入被划分为：

1. **公开输入** $x$（证明者$\mathcal{P}$与验证者$\mathcal{V}$共同知晓）
2. **私有见证** $w$（仅$\mathcal{P}$知晓）

对于给定的公开输入 $x$ 和输出 $y$，该问题的判定条件为：
$$
\exists w \quad \text{s.t.} \quad \mathcal{C}(x,w) = y
$$

---

## 7 第一个电路可满足性（Circuit-SAT）的 SNARK

---

### Succinct Arguments for Circuit Satisfiability

如果一个证明（或论证）系统在验证电路可满足性时，能够避免向验证者发送整个见证 $w$，那么我们称其为**简洁的（Succinct）**。具体来说，当验证电路可满足性的过程中，总通信量相比于见证的规模 $|w|$ 呈现亚线性增长时，我们就认为这个论证系统具有简洁性。

更短的证明总是更好的。例如在某些区块链应用中（ZK-Rollups），证明可能被永久存储在区块链上。如果证明过长，将显著增加整个区块链网络的存储需求。对于许多（但非全部）论证系统而言，更短的证明还能带来更快的验证速度。

另外，从计算机程序到电路可满足性的高效转换过程，往往会产生具有超大见证的电路（参见第6.5节）。

---

### A First Succinct Argument for Circuit Satisfiability

我们将通过 GKR 协议+承诺（commitment）的方式构造一个简洁的论证系统。我们之前介绍的 GKR 协议是用于计算电路求值的，但是我们这里的问题是电路可满足性，即证明者证明他知道一个见证 $w$，使得：

$$
C(x,w)=y
$$

我们用 $W:\{0,1\}^{1+\log n}\to\mathbb F$ 来表示 $x$ 和 $w$ 的特征函数，$W(0,...)$ 表示 $x$，$W(1,...)$ 表示 $w$。证明者在协议的最开始发送 $\tilde{W}$ 的承诺，从而避免发送整个 $w$。

承诺的构造方法有很多，我们在后面的章节会详细讨论。这里我们利用 Merkle Trees 和 Low Degree Tests 构造一个承诺。

在单独使用 Merkle Tree 时，我们将函数 $f:\mathbb F^{\log n}\to\mathbb F$ 的所有取值作为叶子结点，将根节点 $s$ 作为承诺。但是这样我们无法保证 $s$ 对应的函数是一个多重线性函数，所以我们需要进行 Low Degree Tests。

Low Degree Tests 实现很简单。我们让验证者随机选一个线性函数 $\ell:\mathbb F\to\mathbb F^{\log n}$，要求证明者提供 $f\circ\ell$ 的参数，验证其是一个度数不超过 $\log n$ 的函数，且和 $f$ 保持一致性。分析证明比较复杂，这里不详细讨论。

---

### Knowledge-Soundness

