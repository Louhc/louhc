---
title: Proofs, Arguments, and Zero-Knowledge (2)
author: louhc
categories: Reading Notes
date: 2024-07-03 16:55:54
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

# *Proofs, Arguments, and Zero-Knowledge* Reading Notes

## 5 Fiat-Shamir 变换

Fiat-Shamir transformation 可以将任意 public-coin protocol $\mathcal I$ 转换为一个 non-interactive, publicly verifiable protocol $\mathcal Q$。

### The Random Oracle Model

随机函数 $R:D\to\{0,1\}^{\kappa}$，对于任意 $x\in D$，$R$ 从 $\{0,1\}^{\kappa}$ 中均匀随机选取它的值 $R(x)$。

**ROM** 假设证明者和验证者可以访问一个黑盒随机函数 $R$，对于一次访问 $x$，$R(x)$ 将会独立地从 $\{0,1\}^{\kappa}$ 随机均匀地选取一个数并返回，然后记录这个值，下次如果重复查询 $R(x)$ 时返回相同的值。

实际中使用真正的 ROM 是不现实的，一般会使用一些具体的哈希函数（如 SHA-3 等）来代替。

### The Fiat-Shamir Transformation

### Security of the Transformation

---

## 6 将程序转换为 Circuit

---

## 7 Circuit-SAT