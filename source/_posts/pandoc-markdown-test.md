---
title: Pandoc Markdown test
author: louhc
date: 2024-06-24 22:28:48
categories:
tags:
keywords:
description:
mathjax: true
---

\usepackage{amsmath,amssymb,amsthm}

\newcommand{\Enc}{\text{Enc}}

hexo 自带的 markdown 渲染器太拉了，老是和 mathjax 冲突，包括但不限于把公式里的 `/` 和 `_` 认成 markdown 语法

所以改用 pandoc 了

---

1. Hello
2. World

```c++
This is code
```

$\binom{n}{m}=\frac{n!}{m!(n-m)!}$

$$
c\leftarrow \Enc_k(m)
$$