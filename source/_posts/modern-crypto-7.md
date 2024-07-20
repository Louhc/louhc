---
title: Introduction to Modern Cryptography(7)
author: louhc
categories: Reading Notes
date: 2024/6/27 20:17:41
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

## 7 Practical Constructions of Symmetric-Key Primitives

### 流密码

**Linear-Feedback Shift Registers(LFSR)** 

$y_i=s_i^{(0)},i=0,...,n-1$

$y_i=\oplus_{j=0}^{n-1}c_jy_{i-n+j},i>n-1$

$n$ 称为 LFSR 的度数。

知道 $2n$ 个输出后，可以得到 $n$ 个方程（如果是max length的，这 $n$ 个方程是独立的），然后可以解出所有系数 $c$ 的值。

可以通过引入一些非线性函数（比如 AND 和 OR）来避免这种攻击，称为 FSR，比如将状态转移方程替换为一个非线性函数，或者将输出值替换为当前状态的一个非线性函数，或者使用多个 LFSR，将它们的输出的一个非线性函数作为输出。

**Trivium**

**RC4**

**ChaCha20**

---

### 分组密码

**Confusion-diffusion paradigm**

1. *round functions* $\{f_i\}$ introduce *confusion* into $F$
$$
F_k(x) = f_1(x_1)||\cdots||f_{16}(x_{16})
$$
2. introduce *diffusion* using a mixing permutation whereby the bits of the output are permuted, or "mixed"

这样一个 confusion/diffusion 操作称为一个 *round*，重复这样的 round 若干次。

**Substitution-permutation networks (SPN)**

使用一个 "substitution function" $S$ (是一个 permutation)，称为 $S$-box，定义 $f(x)=S(k\oplus x)$

1. *Key mixing*: Set $x:=x\oplus k$, where $k$ is the current-round *sub-key*;
2. *Substitution*: Set $x:=S_1(x_1)||\cdots||S_8(x_8)$, where $x_i$ is the $i$th byte of $x$;
3. *Permutation*: Permute the bits of $x$ to obtain the output of the round.

在最后一个 round，还要经过一个 final key-mixing step，然后将结果作为最终的输出。

**The avalanche effect（雪崩效应）**

1. 修改输入的一个 bit，$S$-box 的输出会变化至少两个 bit。
2. mixing permutations 能使得修改输入的一个 bit 时，多个 $S$-box 的输入会发生变化。

(skip)

**Attacking a one-round SPN** (skip)

**Feistel networks**

与 SPN 使用的 $S$-box 相反，Feistel networks 使用的是一个**不可逆的**函数。

将 $x$ 分为两半 $L_i$ 和 $R_i$，$$L_{i}:=R_{i-1}\text{ and }R_{i}=L_{i-1}\oplus f_{i}(R_{i-1})$$

注意尽管 $f$ 不一定可逆，但是Feistel networks是可逆的。

**Attacking Reduced-Round Feistel Networks** (skip)

**DES - The Data Encryption Standard** (skip)

**3DES** (skip)

**AES - The Advanced Encryption Standard** (skip)

### 压缩函数和哈希函数

**Davies–Meyer construction** 可以使用 block cipher 构造压缩函数，

$$
h(k,x)\overset{\text{def}}{=}F_k(x)\oplus x
$$

然后对 $h$ 使用 Merkle–Damgård 变换。

> If $F$ is modeled as an ideal cipher, then any attacker making $q$ queries to $F$ or $F^{−1}$ can find a collision in the Davies–Meyer construction with probability at most ${q^2}/2^{\ell}$.

（证明 skip）

**MD5** 现在已经被破解的上古遗留哈希（

**SHA-1 (Secure Hash Algorithms)** 不再推荐使用的哈希

**SHA-2** 包含 **SHA-256** 和 **SHA-512**，是目前推荐使用的比较安全的哈希函数。

**SHA-3 (Keccak)** NIST 举办了一场持续几年的哈希函数设计比赛，然后Keccak赢了，他设计的哈希成为了 SHA-3，差不多就是回事（

SHA-3 没有使用 Merkle–Damgård 变换，它的核心 primitive 是一个没有 key 的，block length 为 $1600$ 的 permutaion $P$，它通过 **sponge contruction** 直接构造哈希函数（与之相对的，通常的做法是先构造压缩函数）。如果 $P$ 是随机排列，那么得到的哈希函数是 collision resistant 的。

random permutation model 比 ideal cipher model 要弱，可以定义 $P(x)=F(0^n,x)$，从一个 ideal cipher model 得到一个 random permutation model。

sponge construction 的具体细节

> Fix $P :\{0, 1\}^{\ell} \to \{0, 1\}^{\ell}$ and constants $r, c, v$ as in the text and $\lambda \geq 1$. Hash function $H$, on input $\hat m \in \{0, 1\}^∗$, does:
> 
> **(Padding)** Append a $1$ to $\hat m$ followed by enough zeros so that the length of the resulting string is a multiple of $r$. Parse the resulting string as the sequence of $r$-bit blocks $m_1,...,m_t$.
> 
> **(Absorbing phase)** Set $y_0 := 0^{\ell}$. Then for $i=1,...,t$ do:
> 
> - $x_i:= y_{i−1}\oplus (m_i||0^c)$.
> - $y_i:=P(x_i)$.
>
> **(Squeezing phase)** Set $y_1^∗:=y_t$, and let $h_1$ be the first $v$ bits of $y_1^∗$.Then for $i=2,...,\lambda$ do
> 
> - $y_i^∗:=P(y_{i−1}^∗)$.
> - Let $h_i$ be the first $v$ bits of $z_i^∗$.
> 
> **(Output)** Output $h_1||\cdots||h_{\lambda}$.

简单地说，

1. $y_0=0^n$
2. *absorbing phase* 将 $y_i$ 的前 $r$ 位与 $m_i$ 求异或，$y_i = P(y_{i-1}\oplus m_i)$
3. *squeezing phase* 不断输出 $y_i$ 的前 $v$ 位，$y_i=P(y_{i-1})$

> Let $H$ denote Construction 7.6 (就是 sponge construction) with $\lambda = 1$ (squeezing phase 的长度，决定输出的长度). If $P$ is modeled as a random permutation, then any attacker making $q$ queries to $P$ or $P^{−1}$ can find a collision in $H$ with probability at most $\frac{q^2}{2^v}+\frac{q\cdot(q+1)}2$.

(证明 skip)