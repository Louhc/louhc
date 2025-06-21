---
title: Proofs, Arguments, and Zero-Knowledge (2)
author: louhc
categories: Reading Notes
tags:
 - crypto
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

本篇笔记涵盖 [《Proofs, Arguments, and Zero-Knowledge》](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) 的**第5章** ，**第6章**和**第7章**。

<!-- more -->

## 5 Fiat-Shamir 变换

我们之前提到过**公开随机数协议**（public-coin protocol），即验证者 $\Vrf$ 在生成一个随机数后，$\Prv$ 能立即获知这个随机数。不失一般性地，我们可以认为在公开随机数协议中，$\Vrf$ 向 $\Prv$ 发送的唯一消息就是他生成的随机数。

Fiat-Shamir 变换是一种在零知识证明领域相当重要且广泛应用的技术，可以将任意公开随机数协议 $\mathcal I$ 转换为一个非交互性的，可以公开被验证的协议 $\mathcal Q$。

### The Random Oracle Model

**随机函数**指的是一种特殊的函数 $R:D\to\{0,1\}^{\kappa}$，对于任意 $x\in D$，$R$ 从 $\{0,1\}^{\kappa}$ 中均匀随机选取它的值 $R(x)$。

**随机预言机模型**（Random Oracle Model，ROM）假设证明者和验证者可以访问一个黑盒随机函数 $R$，对于一次访问 $x$，如果之前从未访问过这个值，ROM 会返回 $R(x)$，即独立地从 $\{0,1\}^{\kappa}$ 随机均匀地选取一个数并返回，然后记录这个值，下次如果重复查询 $R(x)$ 时返回相同的值。

现实世界中这样的 ROM 是不存在的，一般会使用一些具体的哈希函数（如 SHA-3 等）来代替。

### The Fiat-Shamir Transformation

### Security of the Transformation

---

## 6 将程序转换为电路

在第4章中，我们提到了 GKR 协议，它是一个关于对算数电路求值的交互式证明协议。但是，现实中人们几乎不会使用算数电路来解决问题，而更偏好用一些高级编程语言如 C++，Python 等。因此，如果想在现实中大规模应用某个协议，我们需要一种技术，将高级编程语言转换为协议能处理的语言（对于 GKR 协议来说就是算术电路）。

---

## 7 第一个电路可满足性（Circuit-SAT）的 SNARK