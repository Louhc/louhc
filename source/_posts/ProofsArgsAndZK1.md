---
title: Proofs, Arguments, and Zero-Knowledge (1)
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

本篇笔记涵盖 [《Proofs, Arguments, and Zero-Knowledge》](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) 的**第3章**和**第4章**，介绍了交互式证明（Ineractive Proofs）和论证系统的形式化定义，介绍了零知识证明领域重要的两个定理：Schwartz-Zippel Lemma 和 Lagrange Interpolation of Multilinear Polynomials。接下来，运用这些定理，给出了包括求和检查协议（The Sum-Check Protocol）等在内的一些具体实例。

<!-- more -->

## 3 定义和技术准备工作

---

### Interactive Proof Systems

*交互证明系统（Interactive Proof Systems，简称 IP Systems）* 中，两方（**证明者** $\Prv$和**验证者** $\Vrf$）进行若干次交互，$\Prv$ 需要向 $\Vrf$ 证明一个 claim，$\Vrf$ 通过 $\Prv$ 提供的信息（以及自身的算力）检验该 claim 的正确性。形式化的定义如下。

给定一个函数 $f:\{0,1\}^n\to\mathcal R$，一个 k-message IP 包含了一个 **PPT** 验证算法 $\Vrf$ 和一个 **确定**（deterministic）证明算法 $\Prv$。$\Vrf$ 和 $\Prv$ 进行如下的交互：

1. 给 $\Prv$ 和 $\Vrf$ 一个共同的 $x\in\{0,1\}^n$。
2. $\Prv$ 给出一个 $y$，声明 $f(x)=y$。
3. $\Prv$ 和 $\Vrf$ 交换一系列消息 $m_1,m_2,...,m_k$，双方交替发送消息（如果 IP 指定 $\Vrf$ 发送 $m_1$，那么 $\Prv$ 发送 $m_2$，$\Vrf$ 发送 $m_3$，以此类推）。$\Vrf$ 和 $\Prv$ 都根据之前交换的一系列消息来计算下一条消息。整个消息序列 $t=(m_1,m_2,...,m_k)$，和 $\Prv$ 声明的 $y$ 一起，被称为 **transcript**。
4. 在协议的末尾，$\Vrf$ 需要输出 $1$ 或者 $0$，表示他是否接受 $f(x)=y$。

用 $\out(\Vrf,x,r,\Prv)$ 来表示 $\Vrf$ 的输出，其中 $r$ 表示 $\Vrf$ 的内部随机性。所以 $\out$ 是一个确定函数。

由于 $\Vrf$ 是一个随机算法，在定义完备性和可靠性时，我们也需要借助概率。

> An interactive proof system $(\Vrf,\Prv)$ is said to have completeness error $\delta_c$ and soundness error $\delta_s$ if the following two properties hold.
>
> - (Completeness) For every $x\in\{0,1\}^n$, $$\Pr_r[\out(\Vrf,x,r,\Prv)=1]\geq1-\delta_c$$
> - (Soundness) For every $x\in\{0,1\}^n$ and every deterministic prover strategy $\Prv'$, if $\Prv'$ sends a value $y \neq f(x)$ at the start of the protocol, then $$\Pr_r[\out(\Vrf,x,r,\Prv')=1]\leq \delta_s$$

当 $\delta_c,\delta_s \leq 1/3$，我们称这个 IP 系统是**有效的**。

关于上面的定义，需要注意以下几点：

- 对于任何满足 $\delta_c\leq\frac13$ 的 IP，都可以以**一个多项式复杂度的增长**为代价转换为 perfect completeness（$\delta_c=0$）。
- $\delta_s\leq\frac13$ 的 $\frac13$ 只是按照习惯选取的，可以通过重复试验 $O(k)$ 次将其降为 $\delta_s^k$。
- $\Vrf$ 的 随机性是**内置**的，即 $r$ 不会被外界（包括 $\Prv$） 知道，这在文献中被称为 *private randomness*。如果随机性是**公开**的，即 $\Vrf$ 每次“抛一个硬币”都能立即被 $\Prv$ 知道结果，这样的 IP 被称为 *public-coin IP*，也是非常有讨论价值的。其实它们之间的差别并不大，任何 private-coin IP 都可以通过 public-coin IP 来模拟（以一个多项式的复杂度增长为代价）。
- 使用确定性证明算法 $\Prv$ 只是为了方便，将其改成随机性并不会影响 IP 的定义。
- 除了 $\Prv$ 和 $\Vrf$ 的时间消耗，他们之间的空间消耗同样值得关注，如果他们交换了 $k$ 条消息，那么称 $\lceil\frac{k}{2}\rceil$ 为 **round complexity**。

**Interactive Proofs for Languages Versus Functions** 至此我们描述的 IP 系统，claim 都是通过一个函数来描述的（$f(x)=y$）。除此之外 claim 当然也可以用 **语言（languages）** 来描述，但是完备性和可靠性的定义有所不同。

- Completeness. For any $x\in\Lang$, there is some prover strategy that will cause the verifier to accept with high probability.
- Soundness. For any $x\notin\Lang$, then for every prover strategy, the verifier rejects with high probability.

主要的区别在于，for languages 版的 IP 对于 $x\notin\Lang$，$\Prv$ 不需要证明 $f_{\Lang}(x)=0$，而原来 for functions 版的 IP 必须证明每一个 $x$ 的 claim $f(x)=y$。

但是 for functions 和 for languages 可以联系起来，一个关于 $f$ 的 IP 可以改写为关于 $\Lang=\{(x,y)\,|\,y=f(x)\}$ 的 IP。

---

### Argument Systems

函数 $f$ 的 **论证系统** (argument systems)，是指 $f$ 的一个交互式证明，其中 **可靠性条件** (soundness condition) 仅需对 **多项式时间运行** 的证明算法 $\Prv\in\mathbf{P}$ 成立。

也就是说，**无限算力**的恶意证明者可能可以做“伪证”。之前 IP 中的 soundness 被称为 *statistical soundness* 或 *information-theoretic soundness*。论证系统有时也被称为 *computationally sound proofs*。

---

### The Power of Interaction

**IP**（交互式证明类）是指所有“存在多项式时间验证者的交互式证明系统”可判定的语言类。值得注意的是，**NP**类可以视为**IP**的一个特例——即限制不允许交互且验证者不能使用随机性的情况。事实上，理论计算机科学中有一个重要结论：**IP = PSPACE**（多项式空间可解决的问题类，可能需指数时间）。由于普遍认为 **PSPACE** 远大于 **NP**，这表明交互式证明系统比传统的**NP**证明系统强大得多。

交互式证明的强大能力源于**随机性**与**交互性**的协同作用：

1. 若去除随机性（即要求可靠性误差 $\delta_s=0$），交互将失去意义——因为证明者 $\Prv$ 可以完全预测验证者 $\Vrf$ 的所有行为；
2. 若仅保留随机性而去除交互性，则得到的是**MA**（Merlin-Arthur）类，这类系统被认为与**NP**等价。

---

### Schwartz-Zippel Lemma

**定理 3.3 (Schwartz-Zippel 引理)**  设 $\mathbb{F}$ 为任意域，$g: \mathbb{F}^m \to \mathbb{F}$ 是一个总次数不超过 $d$ 的 $m$ 元非零多项式。对于任意有限子集 $S \subseteq \mathbb{F}$，当 $x$ 从 $S^m$ 中均匀随机选取时，有：
$$
\Pr_{x \leftarrow S^m}[g(x)=0] \leq \frac{d}{|S|}
$$

这个引理揭示了多元多项式在随机点取零值的概率上界。其核心结论可以理解为：

1. **零点的稀疏性**：非零多项式在随机点处取零值的概率不超过多项式次数与域大小的比值
2. **维度无关性**：上界仅依赖于多项式总次数 $d$ 和采样集大小 $|S|$，与变量个数 $m$ 无关

当 $|S| \gg d$ 时，取零概率趋近于0。实践中常选择 $|S| \approx 2d$ 使错误概率低于50%。该引理在有限域（特别是大特征域）上表现最优，是许多概率证明技术的核心工具。

<details>

<summary>定理证明</summary>

暂略

</details>


---

### Low Degree and Multilinear Extensions

令 $f:\{0,1\}^{v}\to\mathbb F$ 为任意函数，它的**扩展（extension）**是一个有 $v$ 个变量的的多项式 $g$，满足 $g(x)=f(x),\forall x\in\{0,1\}^v$。需要注意的是，$g$ 的取值范围是 $\mathbb F^v$ 而非 $\{0,1\}^v$；在计算 $g(x),x\in\{0,1\}^v$ 时，$0$ 和 $1$ 分别表示 $\mathbb F$ 的加法单位元和乘法单位元。

可以将一个函数 $f$ 的扩展视为它的“距离放大的编码”。如果两个函数 $f,f':\{0,1\}^v\to\mathbb F$ 只在某一点不同，那么它们的次数不超过 $d$ 的扩展 $g,g'$ **几乎在所有点上**都不同，理由是根据 Schwartz-Zippel Lemma，$g(x)-g'(x)$ 等于 $0$ 的概率不超过 $\frac{d}{|\mathbb F|}$。下面我们将说明，我们总能找到一个次数 $d$ 足够小的扩展，使得 $d\ll|\mathbb F|$。

**Fact 3.5** 对于任意函数 $f:\{0,1\}^v\to\mathbb F$，存在一个唯一的**多重线性扩展**（Multilinear Extension，MLE），我们用 $\tilde{f}$ 来表示该扩展。

<details>

<summary> Hint: 多重线性 </summary>

多重线性多项式指的是**每个变量的次数都不大于 1**的多项式。

例如，$x_1x_2+4x_1+3x_2$ 是多重线性的，$x_2^2+4x_1+3x_2$ 不是多重线性的。

</details>

**定理 3.6（多重线性多项式的拉格朗日插值）** 令 $f(x):\{0,1\}^v\to\mathbb F$ 是任意函数，那么下面的多项式 $\tilde{f}$ 是 $f$ 的扩展。

$$
\tilde{f}(x_1,...,x_v)=\sum_{w\in\{0,1\}^v}f(w)\cdot\mathcal{X}_w(x_1,...,x_v)
$$

其中

$$
\mathcal X_w(x_1,...,x_v):=\prod_{i=1}^{v}(x_iw_i+(1-x_i)(1-w_i))
$$

<details>

<summary>简单的证明</summary>

可以看出，当 $x=w$ 时，$\mathcal X_w(x_1,...,x_v)=1$；当 $x\in\{0,1\}^v$ 且 $x\neq w$ 时，$\mathcal X_w(x_1,...,x_v)=0$，因此 $\tilde{f}$ 是 $f$ 的一个扩展。

另外，容易发现，所有的 $\mathcal X_w$ 都是多重线性的，因此 $\tilde{f}$ 也是多重线性的。

我们没有证明 $\tilde{f}$ 的唯一性，但是由于这一性质并不重要，所以略去。

</details>

令 $n=2^v$，给定 $f(w)$ 在 $w\in\{0,1\}^v$ 上的所有取值和一个向量 $v\in\mathbb F^{v}$，存在一个算法 \[CTY11\]，能够用 $O(n\log n)$ 的时间和 $O(\log n)$ 的空间计算 $\tilde{f}(r)$；存在一个算法 \[VSBW13\]，能够用 $O(n)$ 的时间和 $O(n)$ 的空间计算 $\tilde{f}(r)$。

---

## 4 IP 实例

这章主要是一些经典的交互式证明系统例子。

---

### The Sum-Check Protocol

给定一个 $v$ 元多项式 $g$，The Sum-Check Protocol 要求证明者提供

$$H:=\sum_{b_1\in\{0,1\}}\sum_{b_2\in\{0,1\}}\cdots\sum_{b_v\in\{0,1\}}g(b_1,...,b_v)$$

协议过程如下

> - At the start of the protocol, the prover sends a value $C_1$ claimed to equal the value $H$ defined in Equation (4.1).
> - In the first round, $\Prv$ sends the univariate polynomial $g_1(X_1)$ claimed to equal 
> $$\sum_{(x_2,...,x_v)\in\{0,1\}^{v−1}}g(X_1, x_2,...,x_v)$$.
> $\Vrf$ checks that 
> $$C_1 = g_1(0) +g_1(1)$$
> , and that $g_1$ is a univariate polynomial of degree at most $\deg_1(g)$, rejecting if not. Here, $\deg_j(g)$ denotes the degree of $g(X_1,...,X_v)$ in variable $X_j$.
> - $\Vrf$ chooses a random element $r_1\in \Fld$, and sends $r_1$ to $\Prv$.
> - In the $j$th round, for $1 < j < v$, $\Prv$ sends to $\Vrf$ a univariate polynomial $g_j(X_j)$ claimed to equal
> $$\sum_{(x_{j+1},...,x_v)\in\{0,1\}^{v−j}}g(r_1,...,r_{j−1},X_j,x_{j+1},...,x_v)$$.
> $\Vrf$ checks that $g_j$ is a univariate polynomial of degree at most $\deg_j(g)$, and that $g_{j−1}(r_{j−1})=g_j(0)+g_j(1)$, rejecting if not.
> $\Vrf$ chooses a random element $r_j\in\Fld$, and sends $r_j$ to $\Prv$.
> - In Round $v$, $\Prv$ sends to $Vrf$ a univariate polynomial $g_v(X_v)$ claimed to equal $g(r_1,...,r_{v−1},X_v)$. $\Vrf$ checks that $g_v$ is a univariate polynomial of degree at most $\deg_v(g)$, rejecting if not, and also checks that $$g_{v−1}(r_{v−1}) = g_v(0)+g_v(1)$$
> - $\Vrf$ chooses a random element $r_v\in\Fld$ and evaluates $g(r_1,...,r_v)$ with a single oracle query to $g$. $\Vrf$ checks that $g_v(r_v)=g(r_1,...,r_v)$, rejecting if not.
> - If $\Vrf$ has not yet rejected, $\Vrf$ halts and accepts.

---

### #SAT Problem

一个在变量 $x_1,x_2,...,x_n$ 上的**布尔公式（Boolean Formula）**，指的是一棵二叉树，它的每个叶节点标记着一个随机变量 $x_i$ 或者它的非 $\lnot x_i$，每个非叶节点表示它两个儿子的 AND 或者 OR。树的每个结点也叫作**门（gate）**。布尔公式的大小 $S$ 表示叶节点的数量。

**布尔电路（Boolean Circuit）** 和布尔公式几乎一样，但是布尔电路每个门的输出端可以有多条连边，也就是说，一个门的输出值可以被多个下游门使用。

**The #SAT Problem** 对于在 $n$ 个变量上的大小为 $S=\poly(n)$ 的布尔公式 $\phi$，求

$$\sum_{x\in\{0,1\}^n}\phi(x)$$

#SAT 被认为是一个相当困难的问题，甚至求解是否存在 $\phi(x)=1$ 都需要指数时间，但是存在多项式时间验证者的交互式证明系统解决 #SAT。

将 $\phi$ 转换为算数电路（arithmetic circuit），将 AND 转换位 $x\cdot y$，将 OR 转换为 $x+y-x\cdot y$，然后就可以使用 sum-check protocol 了，此时 soundness error 为 $S/|\Fld|$，可以令 $|\Fld|=S^4$。

可以证明 **IP** = **PSPACE**。

---

### The GKR Protocol and Its Efficient Implementation

之前我们关注的问题都是一些困难的问题，如 #SAT，TQBF 等。在这一节中，我们将关注一些更简单的问题，比如 $\mathbf{P}$ 问题（多项式时间），$\mathbf{NC}$ 问题（并行算法），甚至 $\mathbf{L}$ 问题（对数空间）。我们希望 $\Vrf$ 能够更快地验证问题（比起直接计算而言），最好是只比读取输入稍微多花一点时间。另外，我们希望 $\Prv$ 也能有较高的效率，也就是说，如果问题能被随机存取机或者图灵机在 $T$ 时间和 $s$ 空间内解决，那么我们希望 $\Prv$ 能在 $O(T)$ 时间和 $O(s)$ 空间解决问题，或者尽可能接近，至少得是多项式时间的。