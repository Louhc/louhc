---
title: A Pragmatic Introduction to Secure Multi-Party Computation (1)
author: louhc
categories: Reading Notes
date: 2024/7/14 15:28:11
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

## 1 Introduction

### Outsourced Computation

一方拥有数据，想知道对这些数据进行某种运算的结果；另一方接收数据并进行运算，返回结果。这期间第二方不应该知道任何关于输入、输出结果、中间量的任何信息。

**同态加密（homomorphic encryption）**允许对加密数据进行操作。分为部分同态加密（partially homomorphic encryption）和完全同态加密（FHE, fully homomorphic encryption）

### Multi-Party Computation

持有数据的一些人想共同计算一个函数，但是他们不互相信任，也没有可信任的第三方。