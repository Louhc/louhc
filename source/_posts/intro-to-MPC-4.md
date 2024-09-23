---
title: A Pragmatic Introduction to Secure Multi-Party Computation (4) Implementation Techniques
author: louhc
categories: Reading Notes
date: 2024/7/29 20:45:23
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

## 4 Implementation Techniques

### Less Expensive Garbling
