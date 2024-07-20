---
title: Introduction to Modern Cryptography(11)
author: louhc
categories: Reading Notes
date: 2024/6/29 16:55:55
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
\newtheorem{definition}{Definition}
\newtheorem{theorem}{Theorem}
\newtheorem{lemma}{Lemma}
\newtheorem{protocol}{Protocol}

# *Introduction to Modern Cryptography* Reading Notes

## 11 Key Management and the Public-Key Revolution

### 密钥分发和密钥管理

密钥加密主要会遇到三个问题

- key distribution
- storing and managing large numbers of secret keys
- inapplicability of private-key cryptography to open systems

### 密钥分发中心 KDC

密钥分发中心通常是一个可信的第三方。一种做法是，当新加入一个成员时，分发他与其他所有成员之间的密钥。

online 可能是更好的做法，当 $A$ 需要和 $B$ 通信时，向 KDC 发送请求，KDC 像 $A$ 和 $B$ 发送一个 session key（分别使用 $k_A$ 和 $k_B$ 加密），然后 $A$ 和 $B$ 通过 session key 交流。

**Needham–Schroeder protocol** $A$ 发出请求后，KDC 把 $\Enc_{k_A}(k_{session})$ 和 $\Enc_{k_B}(k_{session})$ 发送给 $A$，$A$ 将后者发送给 $B$。一般将 $\Enc_{k_B}(k_{session})$ 称为 *ticket*。

### 密钥交换和 Diffie-Hellman Protocol

> The key-exchange experiment $\text{KE}^{\text{eav}}_{\Adv,\Pi(n)}$:
> 
> 1. Two parties holding $1^n$ execute protocol $\Pi$. This results in a transcript $\text{trans}$ containing all the messages sent by the parties, and a key $k$ output by each of the parties.
> 2. A uniform bit $b\in\{0, 1\}$ is chosen. If $b = 0$ set $\hat k := k$, and if $b = 1$ then choose uniform $\hat k ∈ \{0, 1\}^n$.
> 3. $\Adv$ is given $\text{trans}$ and $\hat k$, and outputs a bit $b'$.
> 4. The output of the experiment is defined to be $1$ if $b' = b$, and $0$ otherwise. (In case $\text{KE}^{\text{eav}}_{\Adv,\Pi(n)}=1$, we say that $\Adv$ succeeds.)

> A key-exchange protocol $\Pi$ is secure in the presence of an eavesdropper if for all probabilistic polynomial-time adversaries $\Adv$ there is a negligible function $\negl$ such that
>
> $$\Pr[\text{KE}^{\text{eav}}_{\Adv,\Pi(n)}=1]\leq1+\negl(n)$$

**Diffie-Hellman Protocol** 实际上就是基于 $k=g^{xy}=(g^x)^y=(g^y)^x$

> - Common input: The security parameter $1^n$
> - The protocol:
> 
> 1. Alice runs $\mathcal G(1^n)$ to obtain $(G, q, g)$.
> 2. Alice chooses a uniform $x\in \Z_q$, and computes $h_A := g^x$.
> 3. Alice sends $(G, q, g, h_A)$ to Bob.
> 4. Bob receives $(G, q, g, h_A)$. He chooses a uniform $y\in \Z_q$, and computes $h_B := g^y$. Bob sends $h_B$ to Alice and outputs the key $k_B := h^y_A$.
> 5. Alice receives $h_B$ and outputs the key $k_A := h^x_B$.

注意，Diffie-Hellman Protocol 会受到 *man-in-the-middle* attacks，即第三方可以截获 Alice 和 Bob 的消息，然后伪装成另一方分别和他们建立会话。