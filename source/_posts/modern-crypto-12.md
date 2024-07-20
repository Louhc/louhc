---
title: Introduction to Modern Cryptography(12)
author: louhc
categories: Reading Notes
date: 2024/6/30 0:51:01
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

## 12 Public-Key Encryption

### 定义

> A public-key encryption scheme is a triple of probabilistic polynomial-time algorithms $(\Gen, \Enc, \Dec)$ such that:
> 
> 1. The key-generation algorithm $\Gen$ takes as input the security parameter $1^n$ and outputs a pair of keys $(pk, sk)$. We refer to the first of these as the public key and the second as the private key. We assume for convenience that $pk$ and $sk$ each has length at least $n$, and that $n$ can be determined from $pk$, $sk$. The public key $pk$ defines a message space $\M_{pk}$.
> 2. The encryption algorithm $\Enc$ takes as input a public key $pk$ and message $m\in \M_{pk}$, and outputs a ciphertext $c$; we denote this by $c \leftarrow \Enc_{pk}(m)$. (Looking ahead, $\Enc$ will need to be probabilistic in order to achieve meaningful security.)
> 3. The deterministic decryption algorithm $\Dec$ takes as input a private key $sk$ and a ciphertext $c$, and outputs a message $m$ or a special symbol $\perp$ denoting failure. We write this as $m:=\Dec_{sk}(c)$.
> 
> It is required that, except with negligible probability over the randomness of $\Gen$ and $\Enc$, we have $\Dec_{sk}(\Enc_{pk}(m))=m$ for any message $m\in \M_{pk}$.

下面定义与之对应的安全测试

> The eavesdropping indistinguishability experiment $\PubK^{\text{eav}}_{\Adv,\Pi(n)}$:
> 
> 1. $\Gen(1^n)$ is run to obtain keys $(pk, sk)$.
> 2. Adversary $\Adv$ is given $pk$, and outputs a pair of equal-length messages $m_0, m_1\in \M_{pk}$.
> 3. A uniform bit $b\in \{0, 1\}$ is chosen, and then a ciphertext $c \leftarrow \Enc_{pk}(m_b)$ is computed and given to $\Adv$. We call $c$ the challenge ciphertext.
> 4. $\Adv$ outputs a bit $b'$. The output of the experiment is $1$ if $b' = b$, and $0$ otherwise. If $b' = b$ we say that $\Adv$ succeeds.

还有安全定义

> A public-key encryption scheme $\Pi=(\Gen, \Enc, \Dec)$ has indistinguishable encryptions in the presence of an eavesdropper if for all probabilistic polynomial-time adversaries $\Adv$ there is a negligible function $\negl$ such that
> $$\Pr[\PubK^{\text{eav}}_{\Adv,\Pi(n)}=1]\leq \frac12+\negl(n)$$

它是等价于于 CPA-secure 的。

接下来考虑到我们需要使用一个公钥进行多次加密

> The LR-oracle experiment $\PubK^{\text{LR-cpa}}_{\Adv,\Pi(n)}$:
> 
> 1. $\Gen(1^n)$ is run to obtain keys $(pk, sk)$.
> 2. A uniform bit $b\in\{0, 1\}$ is chosen.
> 3. The adversary $\Adv$ is given input $pk$ and oracle access to $\text{LR}_{pk,b}(\cdot, \cdot)$.
> 4. The adversary $\Adv$ outputs a bit $b'$.
> 5. The output of the experiment is defined to be $1$ if $b' = b$, and $0$ otherwise. If $\PubK^{\text{LR-cpa}}_{\Adv,\Pi(n)}=1$, we say that $\Adv$ succeeds.

和上面类似，可以定义 **indistinguishable multiple encryptions**。事实上，满足 CPA-secure 的公钥加密方案都是indistinguishable multiple encryptions。（即它们都是等价的）

（证明skip）

公钥方案一般比私钥方案更容易受到选择密文攻击

> The CCA indistinguishability experiment $\PubK^{\text{cca}}_{\Adv,\Pi(n)}$:
> 
> 1. $\Gen(1^n)$ is run to obtain keys $(pk, sk)$.
> 2. The adversary $\Adv$ is given $pk$ and access to a decryption oracle $\Dec_{sk}(\cdot)$. It outputs a pair of messages $m_0, m_1\in \M_{pk}$ of the same length.
> 3. A uniform bit $b\in\{0, 1\}$ is chosen, and then a ciphertext $c\leftarrow \Enc_{pk}(m_b)$ is computed and given to $\Adv$.
> 4. $\Adv$ continues to interact with the decryption oracle, but may not request a decryption of $c$ itself. Finally, $\Adv$ outputs a bit $b'$.
> 5. The output of the experiment is defined to be $1$ if $b' = b$, and $0$ otherwise.

由此定义 **indistinguishable encryptions under a chosen-ciphertext attack**，或称 **CCA-secure**。

如果一个方案是 indistinguishable encryptions under a chosen-ciphertext attack，那么它也是 indistinguishable multiple encryptions under a chosenciphertext attack。

---

### 混合加密

并联私钥加密和公钥加密，得到**混合加密方案**，(1) 选择一个密钥 $k\sim\mathrm{Uniform}$；(2) 将 $k$ 用公钥加密发送出去。一个更为直接的方式是使用一个公钥原语(primitive)，**key-encapsulation mechanism (KEM)**。

> A key-encapsulation mechanism (KEM) is a tuple of probabilistic polynomial-time algorithms $(\Gen, \text{Encaps}, \text{Decaps})$ such that:
> 
> 1. The key-generation algorithm $\Gen$ takes as input the security parameter $1^n$ and outputs a public-/private-key pair $(pk, sk)$. We assume $pk$ and $sk$ each has length at least $n$, and that $n$ can be determined from $pk$.
> 2. The encapsulation algorithm $\text{Encaps}$ takes as input a public key $pk$ (which implicitly defines $n$). It outputs a ciphertext $c$ and a key $k\in\{0, 1\}^{\ell(n)}$ where $\ell$ is the key length. We write this as $(c, k)\leftarrow \text{Encaps}_{pk}(1^n)$.
> 3. The deterministic decapsulation algorithm $\text{Decaps}$ takes as input a private key $sk$ and a ciphertext $c$, and outputs a key $k$ or a special symbol $\perp$ denoting failure. We write this as $k:=\text{Decaps}_{sk}(c)$.
> 
> It is required that with all but negligible probability over the randomness of $\Gen$ and $\text{Encaps}$, if $\text{Encaps}_{pk}(1^n)$ outputs $(c, k)$ then $\text{Decaps}_{sk}(c)$ outputs $k$.

用 KEM 生成的密钥加密数据，称为 **data-encapsulation mechanism (DEM)**，它是一个公钥加密方案。

> Let $\Pi=(\Gen, \text{Encaps}, \text{Decaps})$ be a KEM with key length $n$, and let $\Pi'=(\Gen',\Enc',\Dec')$ be a private-key encryption scheme. Construct a public-key encryption scheme $\Pi^{\text{hy}}=(\Gen^{\text{hy}}, \Enc^{\text{hy}}, \Dec^{\text{hy}})$ as follows:
> 
> - $\Gen^{\text{hy}}$: on input $1^n$ run $\Gen(1^n)$ and use the public and private keys $(pk, sk)$ that are output.
> - $\Enc^{\text{hy}}$: on input a public key $pk$ and a message $m\in\{0, 1\}^∗$ do:
> 
>     1. Compute $(c, k)\leftarrow \text{Encaps}_{pk}(1^n)$.
>     2. Compute $c'\leftarrow \Enc'_k(m)$.
>     3. Output the ciphertext $\langle c, c'\rangle$.
> - $\Dec^{\text{hy}}$: on input a private key $sk$ and a ciphertext $\langle c, c'\rangle$ do:
> 
>     1. Compute $k := \text{Decaps}_{sk}(c)$.
>     2. Output the message $m := \Dec'_k(c')$.

下面定义 KEM 的 CPA-secure

> The CPA indistinguishability experiment $\text{KEM}^{\text{cpa}}_{\Adv,\Pi(n)}$:
> 
> 1. $\Gen(1^n)$ is run to obtain keys $(pk, sk)$. Then $\text{Encaps}_{pk}(1^n)$ is run to generate $(c, k)$ with $k\in\{0, 1\}^n$.
> 2. A uniform bit $b\in\{0, 1\}$ is chosen. If $b = 0$ set $\hat k := k$. If $b = 1$ then choose a uniform $\hat k\in \{0, 1\}^n$.
> 3. Give $(pk, c, \hat k)$ to $\Adv$, who outputs a bit $b'$ . The output of the experiment is defined to be $1$ if $b' = b$, and $0$ otherwise.

当 $\Pr[\text{KEM}^{\text{cpa}}_{\Adv,\Pi(n)}]\leq\frac12+\negl(n)$, 称其为 CPA-secure。

如果 $\Pi$ 是 CPA-secure 的， $\Pi'$ 是 EAV-secure 的（注意这里不要求 $\Pi'$ CPA-secure），那么 $\Pi^{\text{hy}}$ 是 CPA-secure 的。（证明 skip）

但是，如果 $\Pi'$ 不是 CCA-secure 的，那么 $\Pi^{\text{hy}}$ 也不是 CCA-secure 的。（因为即使攻击者无法知道密钥，也可以调用 $\text{Decaps}$ 知晓一些关于解密结果的信息）

先定义 KEM 的 CCA-secure

> The CCA indistinguishability experiment $\text{KEM}^{\text{cca}}_{\Adv,\Pi(n)}$:
> 
> 1. $\Gen(1^n)$ is run to obtain keys $(pk, sk)$. Then $\text{Encaps}_{pk}(1^n)$ is run to generate $(c, k)$ with $k\in\{0, 1\}^n$.
> 2. Choose a uniform bit $b\in\{0, 1\}$. If $b = 0$ set $\hat k := k$. If $b = 1$ then choose a uniform $\hat k\in\{0, 1\}^n$.
> 3. $\Adv$ is given $(pk, c, \hat k)$ and access to an oracle $\text{Decaps}_{sk}(\cdot)$, but may not request decapsulation of $c$ itself.
> 4. $\Adv$ outputs a bit $b'$. The output of the experiment is defined to be $1$ if $b' = b$, and $0$ otherwise.

当 $\Pr[\text{KEM}^{\text{cca}}_{\Adv,\Pi(n)}=1]\leq \frac12+\negl(n)$，称其为 CCA-secure。

如果 $\Pi$ 是 CCA-secure 的， $\Pi'$ 是 CCA-secure 的，那么 $\Pi^{\text{hy}}$ 是 CCA-secure 的。（证明 skip）

---

### CDH/DDH-Based Encryption

The El Gamal encryption scheme 的构造如下

> Let $\mathcal G$ be a polynomial-time algorithm that takes as input $1^n$ and (except possibly with negligible probability) outputs a description of a cyclic group $\mathbb G$, its order $q$ (with $||q||=n$), and a generator $g$. Define a public-key encryption scheme as follows:
> 
> - $\Gen$: on input $1^n$ run $\mathcal G(1^n)$ to obtain $(\mathbb G, q, g)$. Then choose a uniform $x\in \Z_q$ and compute $h:=g^x$. The public key is $\langle \mathbb G, q, g, h\rangle$ and the private key is $\langle \mathbb G, q, g, x\rangle$ . The message space is $\mathbb G$.
> - $\Enc$: on input a public key $pk = \langle\mathbb G, q, g, h\rangle$ and a message $m\in \mathbb G$, choose a uniform $y\in \Z_q$ and output the ciphertext $$\langle g^y, h^y\cdot m\rangle$$
> - $\Dec$: on input a private key $sk = \langle\mathbb G, q, g, x\rangle$ and a ciphertext $\langle c_1, c_2\rangle$ , output $$\hat m := c_2/c_1^x$$

如果 DDH problem 对于 $\mathcal G$ 是难的，那么它是 CPA-secure 的。（证明 skip）

可以将 El Gamal encryption scheme 用于 KEM，可以使用一个哈希函数 $H$ 将 $m$ 映射到一个01串，将 $H(m)$ 作为密钥。其实还有更简便的做法，实际上 $g^{xy}$ 也是无法区分的，可以直接将 $H(g^{xy})$ 作为密钥。

下面给出具体细节（DDH-Based Key Encapsulation）

> Let G be as in the previous section. Define a KEM as follows:
> 
> - $\Gen$: on input $1^n$ run $\mathcal G(1^n)$ to obtain $(\mathbb G, q, g)$. choose a uniform $x\in \Z_q$ and set $h:=g^x$. Also specify a function $H:G\to \{0, 1\}^{\ell(n)}$ for some function $\ell$ (see text). The public key is $\langle \mathbb G, q, g, h, H\rangle$ and the private key is $\langle \mathbb G, q, g, x\rangle$.
> - $\text{Encaps}$: on input a public key $pk = \langle \mathbb G, q, g, h, H\rangle$ , choose a uniform $y\in\Z_q$ and output the ciphertext $g^y$ and the key $H(h^y)$.
> - $\text{Decaps}$: on input a private key $sk =\langle \mathbb G, q, g, x\rangle$ and a ciphertext $c\in \mathbb G$, output the key $H(c^x)$.

同样，可以证明，如果 DDH problem 对于 $\mathcal G$ 是难的，那么它是 CPA-secure KEM。

（后面还有两小节，先 skip）

---

### RSA-Based Encryption

plain RSA 是不安全的。

**Padded RSA**

> Let $\text{GenRSA}$ be as before, and let $\ell$ be a function with $\ell(n) < 2n$. Define a public-key encryption scheme as follows:
> 
> - $\Gen$: on input $1^n$, run $\text{GenRSA}(1^n)$ to obtain $(N, e, d)$. Output the public key $pk=\langle N, e\rangle$, and the private key $sk = \langle N, d\rangle$.
> - $\Enc$: on input a public key $pk=\langle N, e\rangle$ and a message $m\in\{0, 1\}^{||N||−\ell(n)−1}$, choose a uniform string $r\in\{0, 1\}^{\ell(n)}$ and interpret $\hat m := r||m$ as an element of $\Z^∗_N$. Output the ciphertext $$c:=[\hat m^e \mod N]$$
> - $\Dec$: on input a private key $sk = \langle N, d\rangle$ and a ciphertext $c\in Z^∗_N$, compute $$\hat m:=[c^d \mod N]$$, and output the $||N||−\ell(n)−1$ least-significant bits of $\hat m$.

**RSA PKCS #1 v1.5** 全称 RSA Laboratories Public-Key Cryptography Standard (PKCS) #1 version 1.5

对于公钥 $pk=\langle N, e\rangle$, 令整数 $k$ 满足 $2^{8(k-1)}\leq N< 2^{8k}$，消息长度范围是从 $1$ 到 $k-11$，加密方式如下
$$[(\text{0x00}||\text{0x01}||r||\text{0x00}||m)^e\mod N]$$
其中 $r$ 是一个随机的，长度为 $k-D-3$ byte 的，每一个 byte 都不为 $\text{0x00}$ 的串。

不幸的是，PKCS #1 v1.5 不是 CPA-secure，因为它允许使用过短的随机串 $r$。因此我们需要保证 $r$ 的长度至少为 $||N||/e$。但是，它会受到选择密文攻击。

（skip）