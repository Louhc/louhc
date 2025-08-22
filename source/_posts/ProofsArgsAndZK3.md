---
title: Proofs, Arguments, and Zero-Knowledge (3)
date: 2025-06-21 20:30:53
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
\newcommand{\poly}{\text{poly}}

本篇笔记涵盖 [《Proofs, Arguments, and Zero-Knowledge》](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) 的**第8章** ，**第9章**和**第10章**。

<!-- more -->

## 8 多证明者交互式证明与简洁论证

**Multi-prover interactive proofs (MIPs)** 允许验证者 $\mathcal V$ 与多个不被信任的证明者交互，并假设证明者之间无法交流从 $\mathcal V$ 收到的信息。

---

### MIPs: Definition and Basic Results

**定义 8.1（MIP）** 关于语言 $\Lang\subseteq\{0,1\}^*$ 的 **k证明者交互式证明协议**（k-prover interactive proof protocol）涉及 $k+1$ 方，包括 $1$ 个 **PPT** 验证者和 $k$ 个证明者。交互过程产生一个协议交互记录（transcript） $t = (\Vrf(r),\Prv_1,...,\Prv_k)(x)$，其中 $r$ 表示 $\Vrf$ 的内部随机性。验证者最后的输出为 $\out(\Vrf, x,r,\Prv_1,...,\Prv_k)$。

1. **完备性** 存在一组证明者 $(\Prv_1,...,\Prv_k)$，使得对于任意 $x\in\Lang$，$$\Pr[\out(\Vrf, x,r,\Prv_1,...,\Prv_k)=\text{accept}]\geq 1−\delta_c$$
2. **可靠性** 对于任意 $x\notin\Lang$ 和任意一组证明者 $(\Prv'_1,...,\Prv'_k)$, $$\Pr[\out(\Vrf,x,r,\Prv'_1,...,\Prv'_k) = \text{accept}]\leq\delta_s$$

如果 $\delta_c,\delta_s\leq\frac13$，称其为有效的。复杂度集 $\mathbf{MIP}$ 指的是，对于某个 $k=\poly(n)$，拥有有效的 $k$ 证明者交互式证明协议的语言 $\Lang$ 的集合。

**MIP** 与 **IP** 的核心区别在于其**非适应性（non-adaptive）**的验证机制：

1. 在 **IP** 中，单个证明者（Prover）可以基于历史交互记录动态调整响应策略；  
2. 而在 **MIP** 中，验证者（Verifier）将多个**挑战（challenge）**分发给隔离的证明者，每个证明者仅能获取局部信息（类比“隔离审讯”）。  

这种非适应性的交互模式使得 **MIP** 的表达能力等价于**概率多项式时间谕示图灵机（Probabilistic Polynomial-Time Oracle Turing Machine）**可判定的语言类。

> 设 $\Lang$ 为一个语言，$M$ 为一个概率多项式时间谕示图灵机，其中 $M^{\mathcal O}$ 表示 $M$ 可访问谕示 $\mathcal O$。若满足：
>  
> 1. **完备性** $\forall x\in\mathcal{L}$，$\exists\mathcal{O}$ 使 $M^{\mathcal{O}}$ 以概率 $1$ 接受 $x$；
> 2. **可靠性** $\forall x\notin\mathcal{L}$，$\forall\mathcal{O}$, $M^{\mathcal{O}}$ 都以概率 $\geq2/3$ 拒绝 $x$，
> 
> 则存在关于 $\mathcal{L}$ 的双证明者交互式证明系统（2-prover MIP）。

需要注意一点，这里每次访问谕示 $\mathcal O$ 都是与之前的访问独立，也就是说 $\mathcal O$ 是“无记忆的”。因此，我们可以将其视为一个**无限证明者**交互式证明协议，$M$ 作为验证者，每次都向一个新的证明者发送一个挑战，也就是说，满足上面条件的语言的集合就是 $\mathbf{MIP}$。如果我们证明了上面的命题，也就证明了 $\mathbf{MIP}_2=\mathbf{MIP}=\mathbf{MIP}_k(\forall k\geq 2)$（第二个等号基于一个显而易见的结论 $\mathbf{MIP}_2\subseteq\mathbf{MIP}_k\subseteq\mathbf{MIP}$）。这里我用 $\mathbf{MIP}_k$ 来表示拥有 $k$ 证明者交互式证明协议的语言 $\Lang$ 的集合。

2-prover MIP 的构造方法如下：

1. $\Vrf$ 把 $\Prv_1$ 当做 $\mathcal O$，并模拟 $M^{\Prv_1}$。
2. 如果 $M^{\Prv_1}$ 接受了 $x$，就从之前对 $\Prv_1$ 的所有 challenge 中随机选取一个发送给 $\Prv_2$，如果和 $\Prv_1$ 不一致，那么就拒绝 $x$。
3. 独立地重复这样的过程若干次。

完备性显而易见，至于可靠性，由于我们要求一个没有任何前置知识的 $\Prv_2$ 作出与 $\Prv_1$ 一致的回答——这一点只有 $\Prv_1$ 的所有回答都没有使用到交互记录时才能 100% 实现，因此我们可以将每次对 $\Prv_1$ 的访问都视为独立的。

这里我想到了一个问题，那就是由于会重复多次，$\Prv_2$ 会不会根据之前重复时的交互记录，生成下一轮重复的回答呢？这样做应当是没有意义的，因为每次重复都是**独立**的，$\Prv_2$ 得到的挑战也是独立的。但是我暂时没有想到一个正式的证明。

---

### An Efficient MIP For Circuit Satisfaction

对于一个**低深度**电路，有一个简单的双证明者交互证明系统。其在第7章提到的系统基础上，将第二个证明者作为多项式承诺。具体来说，验证者和第一个证明者执行 GKR 协议，证明 $C(x,w)=y$，原来的协议中验证者要求证明者提供 $\widetilde{w}$ 的承诺并打开 $\widetilde{w}(r)$ 的值，现在验证者直接要求第二个证明者提供 $\widetilde{w}(r)$ 的值，并且进行低次测试（Low Degree Tests）。

如果低次测试使用 point-versus-line 测试，验证者随机选一条 $\mathbb F^{\log n}$ 上的包含 $r$ 的直线 $\lambda$，要求第二个证明者提供一个等于 $\widetilde{w}\circ\lambda$ 的单变量 $\log n$ 次多项式，验证者通过该多项式计算 $\widetilde{w}(r)$ 并与第一个证明者声称的 $\widetilde{w}(r)$ 进行比对。 

该协议的一个缺点在于时间和深度成正比（$O(d\log S)$）。更主要的缺点是证明大小很大。接下来我们将介绍一个更高效的协议。

我们要定义一大堆东西~~（bushi）~~。

- $\mathrm{add},\mathrm{mult}:\{0,1\}^{3k}\to\{0,1\}$，指示门是否为加法/乘法门。$\mathrm{add}(a,b,c)=1$ 当且仅当门 $a$ 为加法门且输入为 $b,c$ 的输出，$\mathrm{mult}$ 同理。和 GKR 协议不同，其定义域为**全部**门，而非某一层的门。
- $\mathrm{io}:\{0,1\}^{3k}\to\{0,1\}$，指示门是否为输入/出门。$\mathrm{io}(a,b,c)=1$ 等且仅当 $a$ 为输入门且 $b=c=\mathrm 0$，或者 $a$ 为输出门且 $b,c$ 为该门的两个输入。
- $I_{x,y}:\{0,1\}^{k}\to\mathbb F$，指示输入/出门的值。如果 $a$ 是输入门，$I_{x,y}(a)=x_a$；如果 $a$ 是输出门，$I_{x,y}=y_a$。
- $G_{x,y,W}:\{0,1\}^{3k}\to\mathbb F$，定义如下：
$$
\begin{aligned}
G_{x,y,W}(a,b,c)&=\mathrm{io}(a,b,c)\cdot(I_{x,y}(a)-W(a))\\
&+\mathrm{add}(a,b,c)\cdot(W(a)-(W(b)+W(c)))\\
&+\mathrm{mult}(a,b,c)\cdot(W(a)-W(b)\cdot W(c))
\end{aligned}
$$

**引理 8.3** 当且仅当 $W$ 是一个正确的计算轨迹时，$\forall a,b,c\in\{0,1\}^{3k}:G_{x,y,W}(a,b,c)=0$。（事实上 $a$ 是输出门时，$W(a)$ 的值好像没有用，也就是说值可以乱取）

对于任意多项式 $Z:\mathbb F^k\to\mathbb F$，定义多项式
$$
\begin{aligned}
g_{x,y,Z}(a,b,c)&=\widetilde{\mathrm{io}}(a,b,c)\cdot(\widetilde{I}_{x,y}(a)-Z(a))\\
&+\widetilde{\mathrm{add}}(a,b,c)\cdot(Z(a)-(Z(b)+Z(c)))\\
&+\widetilde{\mathrm{mult}}(a,b,c)\cdot(Z(a)-Z(b)\cdot Z(c))
\end{aligned}
$$
因此当且仅当 $\forall a,b,c\in\{0,1\}^{3k}:g_{x,y,Z}(a,b,c)=0$ 时，$Z$ 是一个正确的计算轨迹的多重线性扩展。

定义 $\beta_{3k}(a,b):\{0,1\}^{3k}\times\{0,1\}^{3k}\to\{0,1\}$，当且仅当 $a=b$ 时，$\beta_{3k}(a,b)=1$。$\beta$ 的多重线性扩展如下：$$\widetilde{\beta}_{3k}(a,b)=\prod_{j=1}^{3k}((1-a_j)(1-b_j)+a_jb_j)$$

定义多项式
$$
p(X):=\sum_{u\in\{0,1\}^{3k}}\widetilde{\beta}(X,u)\cdot g_{x,y,Z}(u)
$$
显然 $p$ 是多重线性的，而且 $p$ 在 $\{0,1\}^{3k}$ 上全部取值为 $0$ 当且仅当 $g_{x,y,Z}$ 全取值为 $0$。运用 Schwartz-Zippel Lemma，随机选一个 $r\leftarrow\mathbb F^{3k}$，如果 $p(r)=0$，那么 $p(X)\equiv 0$ 大概率成立。因此，定义函数
$$
h_{x,y,Z}(Y):=\widetilde{\beta}(r,Y)\cdot g_{x,y,Z}(Y)
$$
可以发现 $\sum_{Y\in\{0,1\}^{3k}}h_{x,y,Z}(Y)=p(r)$。此时，我们找到了一个多项式 $h_{x,y,Z}$，使得
$$
\sum_{u\in\{0,1\}^{3k}}h_{x,y,Z}(u)=0\Longleftrightarrow Z\text{ extends  a correct transcript}
$$

接下来就可以应用Sum-Check Protocol。将 $r\in\mathbb F^{3k}$ 按照位数均分为三份 $r_1r_2r_3$，验证者只需要对 $\widetilde{\beta}_{3k}(r)$，$\widetilde{\mathrm{io}}(r)$，$\widetilde{\mathrm{add}}(r)$，$\widetilde{\mathrm{mult}}(r)$，$\widetilde{I}_{x,y}(r_1)$，$Z(r_1)$，$Z(r_2)$ 和 $Z(r_3)$ 求值即可。除了最后三个，验证者都可以自行计算。但是由于验证者不知道多项式 $Z$，他需要借助证明者的力量来计算 $Z(r_1)$，$Z(r_2)$ 和 $Z(r_3)$。

我们通过 `4.5.2` 的技巧，将其转换为计算 $Z$ 在单独一个点上的取值 $Z(r_4)$，验证者直接要求第二个证明者提供 $Z(r_4)$ 的值，同时进行低次测试，就像这一个 section 开头描述的那样。

---

### A Succinct Argument for Deep Circuits

通过使用任意多项式承诺，可以将上一个 section 提到的 MIP 协议转换为一个针对又深又窄的算数电路的简洁论证。

缺点在于，需要对整个计算轨迹 $\widetilde{W}:\mathbb F^{\log n}\to \mathbb F$ 做一个承诺，而第七章用 GKR 协议直接构造的简洁论证只需要对见证 $\widetilde{w}$ 做一个承诺。

---

### Extension from Circuit-SAT to R1CS-SAT

**中间表示（intermediate representation）** 指的是可以直接应用交互证明或论证系统的计算模型，arithmetic circuit satisfiability 就是一个例子。另一个热门好用的中间表示例子是 **rank-1 constraint system (R1CS)**。

一个 R1CS 实例指定了三个矩阵 $A,B,C\in\Fld^{m\times n}$，如果存在 $z\in\Fld^{n}$ 且 $z_1=1$ 使得 $$(A\cdot z)\circ(B\cdot z)=(C\cdot z)$$ 则称其为可满足的。我们要求 $z$ 的第一个元素 $z_1$ 是 $1$，以避免存在一个平凡的解——零向量。

对于每个 $i$，都存在一个被称为 rank-one constrain 的等式

$$\langle a_i,z\rangle\cdot\langle b_i,z\rangle=\langle c_i,z\rangle$$

"rank-one" 表示它只包含了一次关于两个 $z$ 中元素的线性组合的乘积。

（to be done）

---

### MIP=NEXP

---

## 9 概率可验证证明与简洁论证

---

### PCPs: Definition and Relationship to MIPs

**定义 9.1** 关于语言 $\mathcal{L} \subseteq \{0,1\}^*$ 的**概率可验证证明系统**（Probabilistically Checkable Proof System, PCP）包含一个**概率多项式时间验证者** $\mathcal{V}$，输入 $x$ 并拥有对证明字符串 $\pi \in \Sigma^\ell$ 的谕示访问权限。

该系统为有效的，当且仅当满足：

1. **完备性**  $\forall x \in \mathcal{L}$，$\exists \pi \in \Sigma^\ell$ 使得 $\Pr\left[\mathcal{V}^\pi(x) = \text{accept}\right] \geq 1 - \delta_c$
2. **可靠性**  $\forall x \notin \mathcal{L}$ 及 $\forall \pi \in \Sigma^\ell$，满足 $\Pr\left[\mathcal{V}^\pi(x) = \text{accept}\right] \leq \delta_s$


PCP 和 MIP 紧密相关：**它们之间可以相互转换**。当然，这样的转换也会伴随着巨大的代价。

**引理 9.2** 假设语言 $\mathcal{L} \subseteq \{0,1\}^{*}$ 存在一个 $k$-证明者的 MIP，其中验证者 $\mathcal{V}$ 向每个证明者发送恰好一条消息（每条消息最多包含 $r_Q$ 比特），且每个证明者在响应中最多发送 $r_A$ 比特。那么，$\mathcal{L}$ 存在一个基于字母表 $\Sigma$（大小为 $2^{r_A}$）的 $k$-查询 PCP 系统，其证明长度为 $k \cdot 2^{r_Q}$，且具有与 MIP 相同的验证者运行时间、完备性错误和可靠性错误。

<details>
<summary>证明概要</summary>
对于 MIP 中的每个证明者 $\mathcal{P}_i$，PCP 证明会为 $\mathcal{V}$ 可能发送给 $\mathcal{P}_i$ 的每条消息预留一个条目。该条目等于证明者对该消息的响应。PCP 验证者通过模拟 MIP 验证者的行为，将 PCP 证明条目视为 MIP 中证明者的回答。
</details>

---

### Compiling a PCP Into a Succinct Argument

---

### A First Polynomial Length PCP, From an MIP

---

### A PCP of Quasilinear Length for Arithmetic Circuit Satisfiability

#### Step 1: Reduce to checking that a polynomial vanishes on a designated subspace

首先，将声明“$M(x)=y$”转变为一个等价的电路可满足性问题 $\{\mathcal C,x,y\}$，证明者声称知道一个低次多项式 $Z$ 是一个正确的计算轨迹 $W$ 的扩展。然后像 MIP 一样，找到一个多项式 $g_{x,y,Z}$ 使得
$$
\forall a\in H:g_{x,y,Z}(a)=0\Longleftrightarrow Z\text{ extends a correct transcript for }\{\mathcal C,x,y\}
$$

#### Step 2: Reduce to Checking that a Related Polynomial is Low-Degree

检查 $g_{x,y,W}$ 是否在 $H$ 上消失的过程和 MIP 十分类似。MIP 中我们检查了一个*多重线性多项式*是否在 $\{0,1\}^{3k}$ 消失，现在我们需要检查一个*单变量多项式*是否在一个特定的集合 $H$ 上消失。我们需要用到下面的引理：

**引理 9.3** 设 $\mathbb F$ 是一个域，$H \subseteq \mathbb F$。对于 $d \geq |H|$，一个 $\mathbb F$ 上的 $d$ 次单变量多项式 $g$ 在 $H$ 上消失（即 $g(\alpha) = 0$ 对所有 $\alpha \in H$ 成立）当且仅当多项式 $\mathbb Z_H(t) := \prod_{\alpha \in H}(t - \alpha)$ 整除 $g$。换言之，当且仅当存在一个次数不超过 $d - |H|$ 的多项式 $h^*$，使得 $g = \mathbb Z_H \cdot h^*$。

<details>
<summary>证明</summary>

**方向 1**（充分性）：  
若 $g = \mathbb Z_H \cdot h^*$，则对任意 $\alpha \in H$，有  
$$
g(\alpha) = \mathbb Z_H(\alpha) \cdot h^*(\alpha) = 0 \cdot h^*(\alpha) = 0,
$$
因此 $g$ 在 $H$ 上消失。  

**方向 2**（必要性）：  
若 $g$ 在 $H$ 上消失，则对每个 $\alpha \in H$，多项式 $(t - \alpha)$ 整除 $g(t)$。因此，$\mathbb Z_H(t)$ 作为这些线性因子的乘积，必然整除 $g$。  
</details>

因此证明字符串只需要证明存在一个度数不超过 $d-|H|$ 的多项式 $h^{*}(z)$，使得 $g_{x,y,Z}(z)=\mathbb Z_H(z)\cdot h^{*}(z)$。如果 $\frac{|\mathbb F|}{|H|}$ 足够大，那么验证者就可以随机选取一个 $r\leftarrow\mathbb F$，检查 $$g_{x,y,Z}(r)=\mathbb Z_H(r)\cdot h^{*}(r)$$ 要证明这个式子成立，证明字符串 $\pi$ 需要包含四部分：

1. 对所有 $z\in\mathbb F$，$Z(z)$ 的取值；
2. $Z$ 的次数不超过 $|H|-1$ 的证明 $\pi_Z$；
3. 对所有 $z\in\mathbb F$，$h^{*}(z)$ 的取值；
4. $h^{*}(z)$ 的次数不超过 $d-|H|$ 的证明 $\pi_{h^{*}}$。

验证者对 $Z$ 进行常数次查询后，可以在准线性时间内计算 $g_{x,y,Z}(r)$；可以直接查询 $h^{*}(r)$ 的取值；可以在多对数时间内计算 $\mathbb Z_H(r)$。

#### Step 3: A PCP for Reed-Solomon Testing

PCP构造的核心在于第三步——验证单变量多项式是否为低次多项式。这一任务在文献中被称为**里德-所罗门测试（Reed-Solomon testing）**，因为 RS 码的码字正是由低次单变量多项式的求值结果构成的。

---

## 10 交互式预言机证明

---

### IOPs: Definition and Associated Succinct Arguments

**IOP**（Interactive Oblivious Proof） 是一种 **IP**，验证者在每一轮中无需读取证明者的**整个**消息，而是被赋予对其的**查询访问权限**，这意味着它可以选择查看消息中的任何所需符号，而“成本”仅为一次查询。

任意 IOP 都可以转化为一个非交互性论证。具体地，证明者不再发送完整的消息，而是发送一个 Merkle commitment，然后验证者发送要查询的符号的位置，证明者提供它们的值以及证明；然后，通过 Fiat-Shamir 将协议转化为非交互性的。

---

### Polynomial IOPs and Associated Succinct Arguments

在一个标准的 IOP 中，证明者发送的消息是字符串，但是在多项式 IOP 中，证明者发送的第 $i$ 条消息是函数 $h_i$，其度数不超过 $d_i$。在这章我们假设 $h_i$ 是单变量的，但是它可以是多变量的。

我们假设 $h_i$ 的参数很多，也就是说验证者无法读入所有的参数，只能对任意一点进行求值，即发送 $r$，得到 $h_i(r)$。

---

### A Polynomial IOP for R1CS-satisfiability

**事实 10.1** 设 $F$ 是一个有限域，$H$ 是 $F$ 的一个大小为 $n$ 的乘法子群。那么对于任意次数小于 $|H| = n$ 的多项式 $q$，有 $\sum_{a \in H} q(a) = q(0)\cdot|H|$。由此可得，$\sum_{a \in H} q(a) = 0$ 当且仅当 $q(0) = 0$。  

<details>
<summary>证明</summary>
由乘法群的性质可得，$$\prod_{a\in H}(X-a)=X^n-1$$

对比等式的两边，可以发现 $X^{n-1}$ 的系数 $-\sum_{a\in H}a=0$。

令 $q:X\to X^m$（$1<m<n$） 表示任意单项式。由于有限域的乘法子群是循环群，可以将 $H$ 表示为 $H=\{h,...,h^{n}\}$。因此

$$
\sum_{a\in H}q(a)=\sum_{j=1}^{n}h^{j\cdot m}
$$

而 $h^m$ 是 $h^mH$ 的生成元，且 $h^mH$ 也是乘法子群，因此 $\sum_{j=1}^{n}h^{j\cdot m}=0$。

因此，对于任意多项式 $q$，在 $H$ 上求和时，次数 $\geq1$ 的项都会被消掉，只剩下常数项，即 $q(0)$。因此 $\sum_{a\in H}q(a)=nq(0)$
</details>

对于 $\mathbb F$ 的乘法子群 $H$，定义 $\mathbb Z_H(X)=X^{n}-1$ 表示其零化多项式。

**引理 10.2** $\sum_{a\in H}p(a)=0$ 当且仅当存在多项式 $h^{*}(x)$ 和 $f(x)$，$\mathrm{deg}(h^{*})\leq D-n$ 且 $\mathrm{deg}(f)<n-1$，使得 $$p(X)=h^{*}(X)\cdot Z_H(X)+f(X)\cdot X$$

<details>
<summary>证明</summary>

如果 $p(X)=h^{*}(X)\cdot \mathbb Z_H(X)+f(X)\cdot X$，根据零化多项式的性质，可得 $\sum_{a\in H}p(a)=\sum_{a\in H}f(a)\cdot X$。而 $f(X)\cdot X$ 的次数小于 $n$，而且 $f(0)\cdot 0=0$，因此有 $\sum_{a\in H}p(a)=0$。

另一方面，如果 $\sum_{a\in H}p(a)=0$，将 $p$ 对 $\mathbb Z_H$ 取模得到 $r$，即 $p(X)=h^{*}(X)\cdot \mathbb Z_H(X)+r(X)$，因此 $\sum_{a\in H}p(a)=\sum_{a\in H}r(a)$。其中 $r(X)$ 的次数小于 $n$，因此 $r(0)=0$，进而可以推出 $r$ 的常数项为 $0$，即 $r(X)=f(X)\cdot X$。

</details>

我们可以用`引理 10.2`来构造一个多项式 IOP，证明一个单变量函数在 $H$ 上求和为 $0$。证明者给出函数 $h^{*}$ 和 $f$ 的承诺，验证者随机选取 $r\leftarrow\mathbb F$，验证 $p(r)=h^{*}(r)\cdot \mathbb Z_H(r)+f(r)\cdot r$。可靠性错误概率为 $\frac{\max\{n,D\}}{|\mathbb F|}$。我们称这个协议为**单变量求和协议（univariant sum-check protocol）**。

接下来我们将利用单变量求和协议构建一个证明 R1CS-SAT 的多项式 IOP。对于矩阵 $A,B,C\in\mathbb F^{m\times n}$ 证明者需要证明他直到一个 $z$ 使得 $$Az\circ Bz=Cz$$ 为了简化问题我们假设 $m=n$，假设存在一个 $\mathbb F$ 的乘法子群 $H$ 大小恰好为 $n$。

令 $\hat{z}$ 为 $z$ 的单变量多项式扩展（拉格朗日插值），其度数不超过 $n-1$ 且 $\forall h\in H,\hat{z}(h)=z_h$。类似地，对于 $z_A=Az,z_B=Bz,z_C=Cz$，记 $\hat{z}_A,\hat{z}_B,\hat{z}_C$ 为它们的扩展。

证明者给出 $\hat{z},\hat{z}_A,\hat{z}_B,\hat{z}_C$ 的承诺，验证者需要检查两部分：

$$
\forall h\in H:\hat{z}_A(h)\cdot\hat{z}_B(h)=\hat{z}_C(h)
$$

以及

$$
\forall h\in H,M\in\{A,B,C\}:\hat{z}_M(h)=\sum_{j\in H}M_{h,j}\cdot\hat{z}(j)
$$

**第一部分** 根据 `引理 9.3`，这个式子等价于存在一个度数不超过 $n$ 的函数 $h^{*}$，使得 
$$
\hat{z}_A(X)\cdot\hat{z}_B(X)-\hat{z}_C(X)=h^{*}(X)\cdot\mathbb Z_H(X)
$$ 
证明者给出 $h^*(X)$ 的承诺，验证者随机选取一个 $r\leftarrow\mathbb F$，检查
$$
\hat{z}_A(r)\cdot\hat{z}_B(r)-\hat{z}_C(r)=h^{*}(r)\cdot\mathbb Z_H(r)
$$
由于 $\mathbb Z_H$ 的稀疏性，验证者可以自行计算 $\mathbb Z_H(r)$。

**第二部分** 令 $\hat{M}(x,y)$ 表示 $M_{x,y}$ 的双变量低度数扩展，即 $\hat{M}(x,y)=M_{x,y}$ 且 $\hat{M}$ 的度数不超过 $n$，这样的多项式是**唯一的**。另外，由于 $\hat{z}$ 和 $\hat{z}_M$ 的唯一性，第二部分的式子等价于：
$$
\hat{z}_M(X)=\sum_{j\in H}\hat{M}(X,j)\hat{z}(j)
$$
验证者随机选取 $r\leftarrow\mathbb F$，检查
$$
\hat{z}_M(r)=\sum_{j\in H}\hat{M}(r,j)\hat{z}(j)
$$
令 
$$
q(Y)=\hat{M}(r',Y)\hat{z}(Y)-\hat{z}_M(r')\cdot|H|^{-1}
$$
注意到 $\sum_{j\in H}q(Y)=\sum_{j\in H}\hat{M}(r,j)\hat{z}(j)-\hat{z}_M(r)$，验证者只需检查
$$
\sum_{j\in H}q(Y)=0
$$
此时应用单变量求和协议，最终验证者只需要随机选取 $r''\leftarrow\mathbb F$，确认 $\hat{z}(r''),\hat{z}_M(r')$ 和 $\hat{M}(r',r'')$ 的取值，即可完成证明。

还剩下一个问题，验证者如何快速计算 $\hat{M}(r',r'')$？

（to be done）

---

### FRI and Associated Polynomial Commitments

