---
title: 一些证明
date: 2024-9-18 14:10:01
tags:
    - 数学
categories: 技术
---

这里记录了一些学习过程中遇到的数学证明

## $d | n \Lrarr x^d+1|x^n+1$

### 充分性

即求证：
$$d|n \Rarr x^d + 1 | x^n +1$$

设 $n = kd$，有
$$x^d-1 = (x-1)(x^{d-1} + \cdots + x + 1)$$

$$
\begin{align*}
x^n-1 &= (x-1)(x^{kd-1} + \cdots + x + 1) \\
&= (x-1)(x^{d-1} + \cdots + x + 1)(x^{(k-1)d} + \cdots + x^d + 1) \\
&= (x^d-1)(x^{(k-1)d}+ \cdots + x^d + 1)
\end{align*}
$$

证毕

### 必要性

即求证:  
$$x^d+1 | x^n + 1 \Rarr d|n$$

设 $n = kd+r$，其中 $ 0 \leq r < d$，只需证 $r = 0$

假设 $r \neq 0 $，有：

$$
\begin{align*}
x^n-1 &= x^{kd} x^r - 1 \\
&= (x^{kd}-1) x^r + x^r-1 \\
&= (x^d-1)(x^{kd-1} + \cdots + x + 1)x^r + x^r-1
\end{align*}
$$

也即 $x^n+1$ 被 $x^d-1$ 除的余式是 $x^r -1$

因为 $r \neq 0$，所以 $x^r -1 \neq 0$  
与条件矛盾，假设不成立

所以 $x^d+1 | x^n + 1 \Rarr d|n$ 证毕

