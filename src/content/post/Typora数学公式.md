---
title: latex数学公式
date: 2024-05-10 10:34:39
updated: "10 May 2024"
publishDate: "10 May 2024"
description: "这篇文章介绍了如何在Typora中使用LaTeX语法编写数学公式，包括分数、根号、偏导数、积分等常见数学符号的表示方法。"
tags: ["latex", "math", "typora", "formula"]
---

##### 分数的表示

$$
\frac{1}{2}
$$

$$
\frac {x^2+x+e^x}{\ln x+\sin x}
$$

##### 根号的表示

$$
\sqrt {x+1}
$$

$$
\sqrt[6] {6x+4}
$$

$$
\sqrt[n] {8x-9}
$$

##### 偏导数符号

\partial{}
$$
\frac{\partial{f}}{\partial x}
$$


##### 积分符号

积分：\int_{x_1}^{\infty}
$$
\int_{x_1}^{\infty}
$$

##### 二重积分

\iint{}
$$
\iint_{D}
$$
##### 三重积分

\iiint{}
$$
\iiint_V
$$
##### 环积分

\oint{}
$$
\oint_L
$$
##### 闭合曲面积分

\oiint{}
$$
\oiint_S
$$


##### 希腊字母表示

$$
\alpha
$$

$$
\Alpha
$$

$$
\beta
$$


$$
\Beta
$$

$$
\gamma
$$

$$
\Gamma
$$

$$
\delta
$$

$$
\Delta
$$

$$
\epsilon
$$

$$
\Epsilon
$$

$$
\zeta
$$

$$
\Zeta
$$

$$
\eta
$$

$$
\Eta
$$

$$
\theta
$$

$$
\Theta
$$

$$
\iota
$$

$$
\Iota
$$

$$
\kappa
$$

$$
\Kappa
$$

$$
\lambda
$$

$$
\Lambda
$$

$$
\mu
$$

$$
\Mu
$$

$$
\nu
$$

$$
\Nu
$$

$$
\xi
$$

$$
\Xi
$$

$$
\omicron
$$

$$
\Omicron
$$

$$
\pi
$$

$$
\Pi
$$

$$
\rho
$$

$$
\Rho
$$

$$
\sigma
$$

$$
\Sigma
$$

$$
\tau
$$

$$
\Tau
$$

$$
\upsilon
$$

$$
\Upsilon
$$

$$
\phi
$$

$$
\Phi
$$

$$
\chi
$$

$$
\Chi
$$

$$
\psi
$$

$$
\Psi
$$

$$
\omega
$$

$$
\Omega
$$

| $\Alpha$   | $\alpha$   | $\Iota$    | $\iota$    | $\Rho$     | $\rho$     |
| ---------- | ---------- | ---------- | ---------- | ---------- | ---------- |
| $\Beta$    | $\beta$    | $\Kappa$   | $\kappa$   | $\Sigma$   | $\sigma$   |
| $\Gamma$   | $\gamma$   | $\Lambda$  | $\lambda$  | $\Tau$     | $\tau$     |
| $\Delta$   | $\delta$   | $\Mu$      | $\mu$      | $\Upsilon$ | $\upsilon$ |
| $\Epsilon$ | $\epsilon$ | $\Nu$      | $\nu$      | $\Phi$     | $\phi$     |
| $\Zeta$    | $\zeta$    | $\Xi$      | $\xi$      | $\Chi$     | $\chi$     |
| $\Eta$     | $\eta$     | $\Omicron$ | $\omicron$ | $\Psi$     | $\psi$     |
| $\Theta$   | $\theta$   | $\Pi$      | $\pi$      | $\Omega$   | $\omega$   |

字母上划线

\overline{}
$$
\overline{V}
$$

##### 字母下划线

\underline
$$
\underline{V}
$$
##### **元素的属于和不属于**

\in
$$
\in
$$
\notin
$$
\notin
$$
##### **任意和存在**

\forall
$$
\forall
$$
\exists
$$
\exists
$$
##### **集合并集和交集**

\bigcup
$$
\bigcup
$$
\bigcap
$$
\bigcap
$$
##### **逻辑的且和或**

\land
$$
\land
$$
\lor
$$
\lor
$$
\bigwedge
$$
\bigwedge
$$
\bigvee
$$
\bigvee
$$
##### **分段函数的表示**

```latex
$$
a_{ij} = 
\begin{cases} 
1 & \text{if } \{v_i, v_j\} \text{ is an edge of } G, \\
0 & \text{otherwise.}
\end{cases}
$$
```

$$
a_{ij} = 
\begin{cases} 
1 & \text{if } \{v_i, v_j\} \text{ is an edge of } G, \\
0 & \text{otherwise.}
\end{cases}
$$
##### 行列式

```latex
\begin{matrix}
a & b \\
c & d
\end{matrix}
```

$$
\begin{matrix}
a & b \\
c & d
\end{matrix}
$$
```latex
\begin{pmatrix}
a & b & e & f\\
c & d & g & h
\end{pmatrix}
```

$$
\begin{pmatrix}
a & b & e & f \\
c & d & g & h
\end{pmatrix}
$$
```latex
\begin{bmatrix}
a & b \\
c & d \\
e & f
\end{bmatrix}
```

$$
\begin{bmatrix}
a & b \\
c & d \\
e & f
\end{bmatrix}
$$

**竖线行列式**

\begin{vmatrix}
$$
\begin{vmatrix}
a&b\\
c&d
\end{vmatrix}
$$
##### **叉乘**

\times
$$
\times
$$
##### **向量**

\vec
$$
\vec{a}
$$

##### **取整符号**

\lfloor x \rfloor
$$
\lfloor x\rfloor
$$
\lceil x \rceil
$$
\lceil x \rceil
$$

##### **恒等于**

\equiv
$$
a\equiv b
$$
##### **字母的数学化表示**

\mathcal{}
$$
\mathcal{D1}
$$

##### **字母加尖角**

\hat
$$
\hat{r}
$$

##### **定义符号**

\triangleq
$$
\triangleq
$$

##### **正负号**

\pm
$$
\pm
$$
##### **约等号**

\approx
$$
\approx
$$
##### **成比例符号**

\sim
$$
\sim
$$
##### **约等号变体**

\simeq
$$
\simeq
$$

##### **空格**

普通空格\
$$
a\ b
$$
中等空格\:或\;
$$
a\:b
$$

$$
a\;b
$$

大空格\quad 或\qquad
$$
a\quad b
$$

$$
a\qquad b
$$

自定义空格\hspace{}

自定义插入指定长度的空格，单位为em，cm
$$
a\hspace{5cm}b
$$
负空格:\ !
$$
a\quad\!e
$$
##### 矩阵的转置

^\top
$$
A^\top
$$

##### 倒T表示

\perp
$$
f_\perp
$$


##### 连乘

\prod
$$
\prod
$$

##### **在箭头上写文字**

\xrightarrow  (左箭头是一样的)
$$
\xrightarrow{P}
$$

##### 表示角的度数

90^\circ
$$
90^\circ
$$

##### 波浪号

\tilde{}
$$
\tilde{X}
$$

##### hadamard product

阿达玛积：\odot
$$
A\odot B\\
$$
