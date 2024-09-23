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

## 3 Fundamental MPC Protocols

### Yao's Garbled Circuit Protocol (Yao's GC)

混淆门的产生过程.

> Parameters: Boolean circuit $\mathcal C$ implementing function $\mathcal F$, security parameter $\kappa$.  
>
> GC generation:
>
> 1. *Wire Label Generation*. For each wire $w_i$ of $\mathcal C$, randomly choose wire labels,  
> $$w_i^b = (k_i^b\in_R\{0, 1\}^{\kappa}, p_i^b\in_R\{0,1\})$$
> , such that $p_i^b = 1−p^{1−b}_i$.
> 2. *Garbled Circuit Construction*. For each gate $G_i$ of $\mathcal C$ in topological order:  
>     a. Assume $G_i$ is a $2$-input Boolean gate implementing function $g_i$: $w_c = g_i(w_a, w_b)$, where input labels are $w^0_a = (k^0_a, p^0_a), w^1_a =(k^1_a, p^1_a), w^0_b= (k^0_b, p^0_b), w^1_b= (k^1_b, p^1_b)$, and the output labels are $w^0_c = (k^0_c, p^0_c), w^1_c = (k^1_c, p^1_c)$.  
>     b. Create $G_i$’s garbled table. For each of $2^2$ possible combinations of $G_i$’s input values $v_a, v_b\in\{0, 1\}$, set  
> $$e_{v_a,v_b} = H(k^{v_a}_a|| k^{v_b}_b|| i)\oplus w_c^{g_i(v_a,v_b)}$$
> Sort entries $e$ in the table by the input pointers, placing entry $e_{v_a,v_b}$ in position $\langle p^{v_a}_a , p^{v_b}_b\rangle$.
> 3. Output Decoding Table. For each circuit-output wire $w_i$ (the output of gate $G_j$) with labels $w_i^0 = (k_i^0, p^0_i), w_i^1 = (k_i^1, p_1^i)$, create garbled output table for both possible wire values $v\in\{0, 1\}$. Set  
> $$e_v=H(k_i^v||\text{"out"}||j)\oplus v$$  
> (Because we are xoring with a single bit, we just use the lowest bit of the output of $H$ for generating the above $e_v$.) Sort entries $e$ in the table by the input pointers, placing entry ev in position $p_i^v$. (There is no conflict, since $p^1_i= p^0_i\oplus 1$.)

接下来是 $P_1$ 和 $P_2$ 的具体交互过程.

> Parameters: Parties $P_1$ and $P_2$ with inputs $x\in\{0, 1\}^n$ and $y\in\{0, 1\}^n$ respectively. Boolean circuit $\mathcal C$ implementing function $\mathcal F$.
>
> Protocol:
>
> 1. $P_1$ plays the role of GC generator and runs the algorithm of Figure 3.1. $P_1$ then sends the obtained GC $\hat C$ (including the output decoding table) to $P_2$.
> 2. $P_1$ sends to $P_2$ active wire labels for the wires on which $P_1$ provides input.
> 3. For each wire $w_i$ on which $P_2$ provides input, $P_1$ and $P_2$ execute an Oblivious Transfer (OT) where $P_1$ plays the role of the Sender, and $P_2$ plays the role of the Receiver:
>    (a) $P_1$’s two input secrets are the two labels for the wire, and $P_2$’s
choice-bit input is its input on that wire.
>    (b) Upon completion of the OT, $P_2$ receives active wire label on the wire.
> 4. $P_2$ evaluates received $\hat C$ gate-by-gate, starting with the active labels on
the input wires.
>    (a) For gate $G_i$ with garbled table $T = (e_{0,0},...e_{1,1})$ and active input
labels $w_a = (k_a, p_a), w_b = (k_b, p_b)$, $P_2$ computes active output
label $w_c = (k_c, p_c)$:
> $$w_c = H(k_a || k_b || i)\oplus e_{p_a,p_b}$$
> 5. Obtaining output using output decoding tables. Once all gates of $\hat C$ are evaluated, using “out” for the second key to decode the final output gates, $P_2$ obtains the final output labels which are equal to the plaintext output of the computation. $P_2$ sends the obtained output to $P_1$, and they both output it.

### GMW Protocol

Yao's GC 只能支持只有两方的情况, 如果需要支持不少于三方, 则需要新的技术支持. 但是 **GMW 协议 (Goldreich-Micali-Wigderson (GMW) Protocol)** 天然支持这一点.

先讲只有两方的情况. 不失一般性地, 我们只考虑NOT, XOR 和 AND 三个门. 对于每根导线(即 wire, 表示某个门电路的输入或输出, 用 $w_i$ 来表示), $P_1$ 和 $P_2$ 都将各自持有它的一个**“部分”(share)** $s_i^1$ 和 $s_i^2$, 这根导线的实际值为 $s_i^1\oplus s_i^2$. $P_1$ 和 $P_2$ 最终只需要把最终输出导线的值展示给对方, 他们就都可以通过异或操作得到计算结果.

具体来说, $P_1$ 对于自己的输入 $x_1,...,x_n$, 生成一列随机数 $r_1,...,r_n$, 然后将 $r_1,...,r_n$  发送给 $P_2$ 作为 $P_2$ 的 share, 而将自己的 share 设为 $x_1\oplus r_1,...,x_n\oplus r_n$. 如此, 只需要将双方的 share 进行异或操作, 就可以得到真正的输入值 $r_1\oplus (x_1\oplus r_1)=x_1$. $P_2$ 也进行同样的操作.  
然后双方各自计算逻辑电路 $\mathcal C$. 当遇到 NOT 门时, 直接将自己所持有的 share 取反, 因为 $(\lnot s_i^1)\oplus(\lnot s_i^2)=\lnot(s_i^1\oplus s_i^2)$, 也就是说操作完成后*双方 share 异或的结果*确实等于*输入实际值 NOT 的结果*.  
遇到 XOR 门时, 也是直接将自己持有的两个输入的 share 取异或, 因为 $(s_i^1\oplus s_j^1)\oplus(s_i^2\oplus s_j^2)=(s_i^1\oplus s_i^2)\oplus(s_j^1\oplus s_j^2)$.  
遇到 AND 门时, 就需要双方进行交流了. $P_1$ 生成一个随机数 $r\in_R\{0,1\}$ 作为自己的 share, 然后枚举两根输入导线 $P_2$ 那边的 share 的可能值, 计算出四种情况下输出导线的实际值, 利用 1-out-of-4 OT 将实际值与 $r$ 的异或值发送给 $P_2$.

可以将上述过程拓展为 $n$ 方计算. 对于某一方的一个输入, 该方生成 $n-1$ 个随机数发送给其他方作为他们的 share, 将这些随机数与自己输入的异或值作为自己的 share. NOT 和 XOR 门的操作都与两方情形一样. 对于 AND 门, 由于 $$(\oplus_{k=1}^{n}s_i^k)\land(\oplus_{k=1}^{n}s_j^k)=(\oplus_{k=1}^{n}(s_i^k\land s_j^k))\oplus(\oplus_{k_1\neq k_2}(s_i^{k_1}\land s_j^{k_2}))$$ 可以各自计算 $s_i^k\oplus s_j^k$, 然后 $P_{k_1}$ 和 $P_{k_2}$ 之间通过 1-out-of-4 OT 计算 $s_i^{k_1}\land s_j^{k_2}$ 的两个 share.

### BGW Protocol

BGW protocol 非常依赖于 Shamir sercret shares, 它天然可以处理算数电路.

用 $[v]$ 表示各方持有 $v$ 的 Shamir secret shares. 随机生成一个多项式 $p$, 它的度数不超过 $t$, 并且常数项 $p(0)=v$, $P_i(1\leq i\leq n)$ 持有的 share 为 $p(i)$. $t$ 表示 sharing 的阈值, 即任意 $t$ 个 share 都无法泄露 $v$ 的任何信息. 当知道至少 $t+1$ 方的 share 时, 可以通过高斯消元或拉格朗日插值获取 $p(0)$ 的值.

协议的具体过程如下. 每一方都将持有的输入值 $v$ 的 share $[v]$ 分发给各方. 当遇到加法门时, 每一方都将自己持有的 share 相加即可, 这样得到的多项式 $p=p_1+p_2$ 满足 $p(0)=p_1(0)+p_2(0)$. 当遇到乘法门时, 直接相乘是不可行的, 因为 $p=p_1\cdot p_2$ 的度数超过 $t$, 需要对其进行降次操作. 由拉格朗日插值可知, $$p(0)=\sum_{i=1}^{2t+1}\lambda_i p(i)$$ $P_1,P_2...P_{2t+1}$ 将持有的 $p(i)$ 的 share $[p(i)]$ 分发出去即可.

由于乘法门的存在, 需要满足 $n\geq 2t+1$, 否则可能无法降次.

### MPC From Preprocessed Multiplication Triples

通过预处理, 可以使满足一些性质的协议 (例如上一节提到的 BGW 协议) 在一定程度上规避交流开销.

以 BGW 协议为例, 各方预先分发 $[a]$, $[b]$, $[c]$, 其中 $c=a+b$, 任意 $t$ 方都无法根据自己的 shares 获知 $a,b,c$ 的任何信息. 当遇到乘法门 (输入为 $v_{\alpha},v_{\beta}$) 时, 各方公开 $[v_{\alpha}-a]$ 和 $[v_{\beta}-b]$, 设 $d=v_{\alpha}-a,e=v_{\beta}-b$. $v_{\alpha}\cdot v_{\beta}=(d+a)(e+b)=de+ea+db+c$, 也就是说 $[v_{\alpha}\cdot v_{\beta}]=de+e[a]+d[b]+[c]$, 而加法门是不需要进行交互的, 因此我们实现了只通过两次”公开“操作完成乘法门的计算.

这样的预处理方法适用于满足如下条件的所有协议:

- 加法同态. 对于 $[a]$ 和 $[b]$, 以及一个公开值 $c$, 各方可以在内部计算 $[a+b], [a+c], [ca]$
- 可公开. 参与计算的各方可以选择公开 $[x]$, 使每一方都知道 $x$ 的值.
- 隐私性. 任何攻击者无法从 $[a]$ 中获取 $a$ 的任何信息.
- Beaver 三元组. 对于每一个乘法门, 各方拥有一个三元组 $[a],[b],[c]$, 其中 $c=ab$.
- 随机输入组件. 对于 $P_i$ 的某个输入 $x$, $P_i$ 将自己产生的一个随机数 $r$ 分发出去 $[r]$, 然后公开 $\delta=x-r$, 即可实现 $x$ 的分发.

### Constant-Round Multi-Party Computation: BMR

> Parameters: Boolean circuit $\mathcal C$ implementing function $\mathcal F$.  
> Let $F : id, \{0, 1\}^{\kappa+1}\to\{0, 1\}^{n\cdot(\kappa+1)}$ be a PRF.  
> Players: $P_1,P_2,...,P_n$ with inputs $x_1,...,x_n\in\{0, 1\}^k$.  
> GC generation:
>
> 1. For each wire $w_i$ of $\mathcal C$, each $P_j$ randomly chooses wire sublabels,$w_{i,j}^b= (k_{i,j}^b, p_{i,j}^b)\in_R\{0, 1\}^{\kappa+1}$, such that $p_{i,j}^b= 1 − p_{i,j}^{1−b}$, and flip bit shares $f_{i,j}\in_R\{0, 1\}$. For each wire $w_i,P_j$ locally computes its underlying-MPC input,
> $$I_{i,j} = (F(i, w_{i,j}^0), F(i, w_{i,j}^1), p^0_{i,j}, f_{i,j})$$
> 2. For each gate $G_i$ of $\mathcal C$ in parallel, all players participate in $n$-party MPC to compute the garbled table, taking as input all players’ inputs $x_1,...,x_n$ as well as pre-computed values $I_{i,j}$, by evaluating the following function:
>     1. Assume $G_i$ is a $2$-input Boolean gate implementing function $g$, with input wires $w_a, w_b$ and output wire $w_c$.
>     2. Compute pointer bits $p^0_a =\oplus_{j=1..n}p^0_{a,j},p^0_b =\oplus_{j=1..n}p^0_{b,j},p^0_c =\oplus_{j=1..n}p^0_{c,j}$, and set $p^1_a = 1 − p^0_a, p^1_b= 1 − p^0_b, p^1_c = 1 − p^0_c$. Similarly compute flip bits $f_a,f_b,f_c$ by xor-ing the corresponding flip bit shares submitted by the parties. Amend the semantics of the wires according to the flip bits by xor-ing $f_a, f_b, f_c$ in the label index as appropriate (included in the next steps).
>     3. Create $G_i$’s garbled table. For each of 2^2 possible combinations of $G_i$’s input values, $v_a,v_b\in\{0,1\}$, set
> $$e_{v_a,v_b} = w^{v_c\oplus f_c}_c\oplus_{j=1..n}(F(i,w^{v_a\oplus f_a}_{a,j})\oplus F(i, w^{v_b\oplus f_b}_{b,j}))$$
> ,where $w^0_c = w^0_{c,1}||...||w^0_{c,n}||p^0_c$, $w^1_c=w^1_{c,1}||...||w^1_{c,n}||p^1_c$. Sort entries $e$ in the table, placing entry $e_{v_a,v_b}$ in position $(p^{v_a}_a , p^{v_b}_b)$.
>     4. Output to $P_1$ the computed garbled tables, as well as active wire labels inputs of $\mathcal C$, as selected by players’ inputs, $x_1,...,x_n$.

### Information-Theoretic Garbled Circuits

### Oblivious Transfer

一个基础的 public key-based OT: $\mathcal R$ 生成一个密钥公钥对 $(sk,pk)$ 和一个随机数 $r$, 然后发送 $(pk, r)$ 或者 $(r, pk)$, $\mathcal S$ 接收到 $(pk_0,pk_1)$ (他不知道哪个是 $pk$, 哪个是 $r$), 发送 $(\Enc_{pk_0}(x_0),\Enc_{pk_1}(x_1))$. 它对于 semi-honest 是安全的, 但是攻击者可以生成两对密钥公钥, 将两个公钥发送出去, 从而同时获知 $x_0,x_1$.

Beaver 提出了一种初始化方法, 可以通过较少次数的公钥加密操作, 实现多项式次 OT. 设 $m$ 为一个批次(batch)的 OT 次数, 我们将使用 $\kappa$ 次 OT 操作完成一个批次的 $m$ 个 OT 操作.

$\mathcal R$ 生成一个 $\kappa$ bit 的随机数 $r$, 利用 psuedorandom generator $G$ 生成长度为 $m$ 的伪随机数. 设 $\mathcal R$ 的 $m$ 次 OT 选择为 $b=b_1...b_m$. $\mathcal R$ 将 $G(r)\oplus b$ 的值发给 $\mathcal S$. 此时 $\mathcal S$ 和 $\mathcal R$ 通过 Yao's GC 共同计算一个函数 $F$, $F$ 的输入为 $r$ 和 $\mathcal S$ 在 $m$ 次 OT 准备的值 $(x_1^0,x_1^1),...,(x_m^0,x_m^1)$, $F$ 根据 $G(r)\oplus b$ 的值获取 $b$ 并计算该返回给 $\mathcal R$ 的值. 需要注意的一点是, $\mathcal R$ 不执行最后一步, 即将计算值发送给 $\mathcal S$.

(skip)
