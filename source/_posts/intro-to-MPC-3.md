---
title: A Pragmatic Introduction to Secure Multi-Party Computation (3) 基础MPC协议
author: louhc
categories: Reading Notes
date: 2024/7/12 23:14:11
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

## 3 Fundamental MPC Protocols

### 姚氏混淆电路协议（Yao's GC）

