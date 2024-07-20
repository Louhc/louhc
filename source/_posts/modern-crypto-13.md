---
title: Introduction to Modern Cryptography(13)
author: louhc
categories: Reading Notes
date: 2024/7/1 0:03:11
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
\newcommand{\PubK}{\text{PubK}}
\newtheorem{definition}{Definition}
\newtheorem{theorem}{Theorem}
\newtheorem{lemma}{Lemma}
\newtheorem{protocol}{Protocol}

# *Introduction to Modern Cryptography* Reading Notes

## 13 Digital Signature Schemes

### 定义

> A (digital) signature scheme consists of three probabilistic polynomial-time algorithms $(\Gen, \text{Sign}, \text{Vrfy})$ such that:
> 
> 1. The key-generation algorithm $\Gen$ takes as input a security parameter $1^n$ and outputs a pair of keys $(pk, sk)$. These are called the public key and the private key, respectively. We assume that $pk$ and $sk$ each has length at least $n$, and that $n$ can be determined from $pk$ or $sk$.
> 2. The signing algorithm $\text{Sign}$ takes as input a private key $sk$ and a message $m$ from some message space (that may depend on $pk$). It outputs a signature $\sigma$, and we write this as $\sigma\leftarrow \text{Sign}_{sk}(m)$.
> 3. The deterministic verification algorithm $\text{Vrfy}$ takes as input a public key $pk$, a message $m$, and a signature $\sigma$. It outputs a bit $b$, with $b = 1$ meaning valid and $b = 0$ meaning invalid. We write this as $b := \text{Vrfy}_{pk}(m, \sigma)$.
> 
> It is required that except with negligible probability over $(pk, sk)$ output by $\Gen(1^n)$, it holds that $\text{Vrfy}_{pk}(m, \text{Sign}_{sk}(m)) = 1$ for every (legal) message $m$. 
> 
> If there is a function $\ell$ such that for every $(pk, sk)$ output by $\Gen(1^n)$ the message space is $\{0, 1\}^{\ell(n)}$, then we say that $(\Gen, \text{Sign}, \text{Vrfy})$ is a signature scheme for messages of length $\ell(n)$.

> The signature experiment $\text{Sig-forge}_{\Adv,\Pi(n)}$:
> 
> 1. $\Gen(1^n)$ is run to obtain keys $(pk, sk)$.
> 2. Adversary $\Adv$ is given $pk$ and access to an oracle $\text{Sign}_{sk}(\cdot)$. The adversary then outputs $(m, \sigma)$. Let $\mathcal Q$ denote the set of all queries that $\Adv$ asked its oracle.
> 3. $\Adv$ succeeds if and only if (1) $\text{Vrfy}_{pk}(m, \sigma) = 1$ and (2) $m \notin \mathcal Q$. In this case the output of the experiment is defined to be $1$.

> A signature scheme $\Pi=(\Gen,\text{Sign},\text{Vrfy})$ is existentially unforgeable under an adaptive chosen-message attack, or just secure, if for all probabilistic polynomial-time adversaries $\Adv$, there is a negligible function $\negl$ such that:
> $$\Pr[\text{Sig-forge}_{\Adv,\Pi}(n) = 1]\leq\negl(n)$$

与 MAC 同理，可以定义**强**安全。

---

### Hash-and-Sign

对于任意长度的消息，可以先对它用哈希映射到一个固定长度的串，然后对其进行数字签证。（和 Hash-and-MAC 差不多）

> Let $\Pi=(\Gen,\text{Sign},\text{Vrfy})$ be a signature scheme for messages of length $\ell(n)$, and let $\Pi_H=(\Gen_H, H)$ be a hash function with output length $\ell(n)$. Construct signature scheme $\Pi'=(\Gen',\text{Sign}' ,\text{Vrfy}')$ as follows:
> 
> - $\Gen'$: on input $1^n$, run $\Gen(1^n)$ to obtain $(pk, sk)$ and run $\Gen_H(1^n)$ to obtain $s$; the public key is $\langle pk, s\rangle$ and the private key is $\langle sk, s\rangle$.
> - $\text{Sign}'$: on input a private key $\langle sk, s\rangle$ and a message $m\in\{0, 1\}^∗$, output $\sigma\leftarrow \text{Sign}_{sk}(H_s(m))$.
> - $\text{Vrfy}'$: on input a public key $\langle pk, s\rangle$, a message $m\in \{0, 1\}^∗$, and a signature $\sigma$, output $1$ if and only if $\text{Vrfy}_{pk}(H_s(m), \sigma)\overset{?}{=}1$.

----

### 基于 RSA 的数字签证

plain RSA signatures 是**不安全的**。

**RSA-FDH**

> Let $\text{GenRSA}$ be as in the previous sections, and construct a signature scheme as follows:
> 
> - $\Gen$: on input $1^n$, run $\text{GenRSA}(1^n)$ to compute $(N, e, d)$. The public key is $\langle N, e\rangle$ and the private key is $\langle N, d\rangle$. As part of key generation, a function $H:\{0, 1\}^∗\to\Z^∗_N$ is specified, but we leave this implicit.
> - $\text{Sign}$: on input a private key $\langle N, d\rangle$ and a message $m\in\{0, 1\}^∗$, compute $\sigma:=[H(m)^d\mod N]$.
> - $\text{Vrfy}$: on input a public key $\langle N, e\rangle$, a message $m$, and a signature $\sigma$, output $1$ if and only if $\sigma^e\overset{?}{=} H(m) \mod N$.

并没有已知的 $H$ 使上面的构造方法是安全的，但是如果令 $H$ 是一个 random oracle，则可以证明安全性，这样得到的加密方案称为 **RSA full-domain hash (RSA-FDH)**。（证明 skip）

---

### 基于离散对数问题的数字签证

**Identification Scheme**

我们只考虑一种三轮的身份证明方案。prover 调用 $\mathcal P_1(sk)$ 生成 $(I,\text{st})$，将 $I$ 发送给 verifier，verifier 从 $\Omega_{pk}$ 中均匀地选取一个 $r$ 发送给 prover，然后 prover 计算 $\mathcal P_2(sk, \text{st}, r)$ 得到 $s$，verifier 验证 $\mathcal V(pk,r,s)\overset{?}{=}I$。

> The identification experiment $\text{Ident}_{\mathcal A,\Pi(n)}$:
> 
> 1. $\Gen(1^n)$ is run to obtain keys $(pk, sk)$.
> 2. Adversary $\Adv$ is given $pk$ and access to an oracle $\text{Trans}_{sk}$ that it can query as often as it likes.
> 3. At any point during the experiment, $\Adv$ outputs a message $I$. A uniform challenge $r\in \Omega_{pk}$ is chosen and given to $\Adv$, who responds with some $s$. ($\Adv$ may continue to query $\text{Trans}_{sk}$ even after receiving $r$.)
> 4. The experiment outputs $1$ if and only if $\mathcal V(pk, r, s)\overset{?}{=} I$.

如果 $\Pi$ 满足 $\Pr[\text{Ident}_{\mathcal A,\Pi(n)}=1]\leq\negl(n)$，那么它就是 **secure against a passive attack** 的。

**Fiat-Shamir Transform** 将一个 identification scheme 转换为一个 signature scheme。

> Let $(\Gen_{\text{id}}, \mathcal P_1, \mathcal P_2, \mathcal V)$ be an identification scheme, and construct a signature scheme as follows:
> 
> - $\Gen$: on input $1^n$, simply run $\Gen_{\text{id}}(1^n)$ to obtain keys $pk, sk$. The public key $pk$ specifies a set of challenges $\Omega_{pk}$. As part of key generation, a function $H:\{0, 1\}^∗\to \Omega_{pk}$ is specified, but we leave this implicit.
> - $\text{Sign}$: on input a private key $sk$ and a message $m\in\{0, 1\}^∗$, do:
> 
>    1. Compute $(I,\text{st}) \leftarrow \mathcal P_1(sk)$.
>    2. Compute $r := H(I, m)$.
>    3. Compute $s := \mathcal P_2(sk,\text{st}, r)$.
> 
>    Output the signature $(r, s)$.
> - $\text{Vrfy}$: on input a public key $pk$, a message $m$, and a signature $(r, s)$, compute $I:=\mathcal V(pk, r, s)$ and output $1$ if and only if $H(I, m)\overset{?}{=}r$.

如果 $\Pi$ 是安全的，$H$ 是一个 random oracle，那么通过 Fiat-Shamir transform 构造的 $\Pi'$ 也是安全的。（证明 skip）

**The Schnorr Identification/Signature Schemes**

1. prover 调用 $\mathcal G(1^n)$ 得到 $(\mathbb G,q,g)$ (其中群的阶 $q$ 满足 $||q||=n$)，均匀随机选取 $x\in\Z_q$，令 $y=g^x$，公钥为 $\langle\mathbb G, q, g, y\rangle$
2. prover 均匀随机选取 $k\in\Z_q$，令 $I=g^k$，将 $I$ 发送给 verifier
3. verifier 选取 $r\in\Z_q$
4. prover 发送 $s=[rx+k\mod q]$
5. verifier 验证 $g^sy^{-r}=I$

如果 discrete-logarithm problem 是难的，那么 Schnorr identification scheme 是安全的。（证明 skip）

**DSA and ECDSA**

**Digital Signature Algorithm (DSA)** 和 **Elliptic Curve Digital Signature Algorithm (ECDSA)** 是使用不同的群的，基于 discrete-logarithm problem 的数字签证算法。

> Consider the following identification scheme in which the prover’s private key is $x$ and public key is $(\mathbb G, q, g, y)$ with $y=g^x$:
>
> 1. The prover chooses uniform $k\in\Z^∗_q$ and sends $I:=g^k$.
> 2. The verifier chooses and sends uniform $\alpha,r\in\Z_q$ as the challenge.
> 3. The prover sends $s:=[k^{−1}\cdot(\alpha+xr)\mod q]$ as the response.
> 4. The verifier accepts if $s\neq 0$ and $g^{αs^{−1}}\cdot y^{rs^{−1}}\overset{?}{=}I$.

> Let $\mathcal G$ be as in the text.
> 
> - $\Gen$: on input $1^n$, run $\mathcal G(1^n)$ to obtain $(\mathbb G, q, g)$. Choose uniform $x\in \Z_q$ and set $y:=g^x$. The public key is $\langle\mathbb G, q, g, y\rangle$ and the private key is $x$. As part of key generation, two functions $H:\{0, 1\}^∗\to\Z_q$ and $F:\mathbb G\to\Z_q$ are specified, but we leave this implicit.
> - $\text{Sign}$: on input the private key $x$ and a message $m\in\{0, 1\}^∗$, choose uniform $k\in\Z^∗_q$ and set $r := F(g^k)$. Then compute $s:=[k^{−1}\cdot(H(m)+xr)\mod q]$. (If $r = 0$ or $s = 0$ then start again with a fresh choice of $k$.) Output the signature $(r, s)$.
> - $\text{Vrfy}$: on input a public key $\langle\mathbb G, q, g, y\rangle$, a message $m\in\{0, 1\}^∗$, and a signature $(r, s)$ with $r,s=0\mod q$, output $1$ if and only if $r\overset{?}{=} F(g^{H(m)\cdot s−1}y^{r\cdot s^{−1}})$.

对于 DSA，$\mathbb G$ 是 $\Z_p^*$ 的一个 $q$ 阶子群，$F(I)\overset{\text{def}}{=}[I\mod q]$；对于 ECDSA，$\mathbb G$ 是椭圆曲线群 $E(\Z_p)$ 的一个 $q$ 阶子群，由于群内元素可以表示为 $\Z_p\times\Z_p$，$F((x,y))\overset{\text{def}}{=}[x\mod q]$。

---

### 安全公钥分发

（skip）

---

### TLS

**Transport Layer Security (TLS)** protocol 使用 `https` 时使用的协议

TLS 协议可以让用户和服务器共享一个密钥集，使用其中的密钥来加密、认证会话内容。它包括 handshake protocol 和 record-layer protocol，前者用于交换密钥，后者用于加密、认证会话。

（skip）

---

### Signcryption

（skip）