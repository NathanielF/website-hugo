---
title: "Why is OLS BLUE?"
author: "Nathaniel Forde"
date: "2016-11-06T13:00:25+05:30"
draft: no
type: post
image: "/portfolio/OLS/OLSThreeDimensions.jpg"
---

There is an aesthetic discipline to mathematics as much as there is an expectation of rigour. We shall attempt to showcase the relation between aesthetic dimension of a proof with its explanatory purchase. We shall take as our example a standard proof used in regression analysis to find the slope of a line/plane which maps the trajectory of a series of observed points/locations along a trajectory. Given this problem we can draw a line/plane with the slope and it can be shown that this line (as a function of slope) is the best available line that can be drawn through our points of observation. But this method fails to connect the proof to the geometric setting of the problem statement. It proves a cheap equality without explanatory power.

$\text{ "If one proves the equality of two numbers } a \text{ and } b \text{ by showing}$
$\text{ first that “} a \text{ is less than or equal to } b\text{” and then}$
$\text{“}a \text{ is greater than or equal to } b \text{”,it is unfair, one should}$
$\text{ instead show that they are really equal by disclosing the inner ground }$
$\text{ for their equality." — Emmy Noether }$

Taking Noether's aesthetic principle as our motivation we shall demonstrate the distinction in relation to the Ordinary Least Squares solution for a generic regression problem. First we will see a quick, but unenlightening proof and then we shall elaborate a more instructive proof of the same. Ultimately we shall then use our result to prove the Gauss Markov theorem. The Linear model:

$$ Y = X\hat{\beta} + \epsilon $$
assumes that $Y$ can be estimated by a linear combination of $X$ with a series of unknown weights $\beta$ where $$SSE = e'e = \lVert Y - X\hat{\beta} \rVert^2 = Y'Y - 2\hat{\beta}'X'Y + \hat{\beta}'X'X\hat{\beta}$$

where the goal is to find $\beta$ to minimise SSE.

The OLS solution  is $\hat{\beta} = (X'X)^{-1}X'Y$.Why?

### The beta estimate: One Answer

Proof: $\hat{\beta} = (X'X)^{-1}X'Y$ minimises SSE.

 - Note that: $\frac{\partial \hat{\beta}'(X'Y)}{\partial \hat{\beta}} = X'Y \text{ and } \frac{\partial \hat{\beta}'X'X\hat{\beta} }{\partial \hat{\beta}} = 2X'X\hat{\beta}$
 - Set:  $\frac{\partial(e'e)}{\partial \hat{\beta}} = \frac{ \partial [Y'Y - 2\hat{\beta}'X'Y + \hat{\beta}'X'X\hat{\beta}]}{\partial \hat{\beta}} = 0 - 2X'Y + 2X'X\hat{\beta} \text{ := }0$
 - $\Rightarrow X'Y = X'X\hat{\beta}$
 - $\Rightarrow (X'X)^{-1}X'Y = \hat{\beta}$

This proof is too quick, moreover it relies on knowledge of matrix calculus. In itself not a sin, but the use made of matrix calculus is not illustrative of any structural relationship between the components of our equation. Why have discovered that $\hat{\beta}$ is the magnitude that minimises the square loss function, but we have not seen why this must be the case.

### Linear Algebra: OLS Preliminaries

Suppose X is $n \times p$ with rank p < n and $( Y\_{n \times 1}$. Let $ \hat{\beta} = (X^{'}X)^{-1}X^{'}Y $ and  $e = Y - X\hat{\beta}$, we shall show algebraically that $\hat{\beta} = \text{arg } \text{min}(SSE)$ and that $\hat{\beta}$ is $BLUE$.

$$X'X  = \sum\_{k= 1}^{n} x'\_{pn}x\_{np}$$

$$\hat{\beta} = (X'X)^{-1}\_{p \times p}X'\_{p \times n}Y\_{n \times 1} = [(X'X)^{-1}X'Y]\_{p \times 1}$$

$$X\_{n \times p}\hat{\beta}\_{p \times 1} = X\hat{\beta}\_{n \times 1}$$

$$e\_{n \times 1} = [Y\_{n \times 1} - (X \hat{\beta}\)_{n \times 1}] $$

In the simple case we have the following image:

<img src ="/projects/OLS/simpleOLS.jpg" style="width: 500px;/img>

### Orthogonal Vectors

In an analogy with the Pythagorean theorem we have two orthogonal (perpindicular) vectors whenever the squared norm (distance) between them is such that $$\lVert v \rVert^2 + \lVert w \rVert^2 = \lVert v - w \rVert^2$$
$$ a^2 + b^2 = c^2$$

- $(v\_{1}^{2} + v\_{2}^{2} ...) + (w\_{1}^{2} + w\_{2}^{2} ....) = (v\_{1} - w\_{1})^2 +...$
- $(v\_{1}^{2} + v\_{2}^{2} ...) + (w\_{1}^{2} + w\_{2}^{2}...) + ... =  (v\_{1}^{2} - 2v\_{1}w\_{1} + w\_{1}^2) + ...$
- $\Rightarrow (0) + (0) .. =   - 2v\_{1}w\_{1} +  - 2v\_{2}w\_{2} ... \Rightarrow 0 = \textbf{v} \cdot \textbf{w}.$

$\textbf{Definition}: \textbf{v}$ is orthogonal to $\textbf{w} \Leftrightarrow \textbf{v} \cdot \textbf{w} = 0$

$\textbf{w}$ is orthogonal to $X \Leftrightarrow \forall(\textbf{x} \in X) \textbf{w} \perp \textbf{x}$

### The Triangle Inequality
If $A, B$ are $n \times 1$ then  $(L3): \lVert A - B \rVert^{2} = \lVert A \rVert^{2} - \lVert B \rVert^2 \Leftrightarrow A \perp B$

Proof (Triangle): First note
$$A'B = a^{'}\_{1}b\_{1} + a^{'}\_{2}b\_{2} + .... + a^{'}\_{n}b\_{n} = \sum\_{i = 1}^{n}a^{'}\_{i}b\_{i} = \sum\_{i = 1}^{n} b^{'}\_{i}a\_{i} = B'A$$
Hence:

- $\lVert A - B \rVert^{2} = (A - B)^{'}(A - B)$
- $= A' + (-B)'(A + (-B))$
- $= A'A - A'B - B'A - B'B$
- $\Rightarrow A'A - B'B - 2A'B =  \lVert A \rVert^{2} - \lVert B \rVert^2 \Leftrightarrow A \perp B$


## The Heart of the Proof


![The Pythagorean Relation](/projects/OLS/Pythagorean.gif)

### Orthogonal Errors
(T1)   $$ \hat{\beta} = (X'X)^{-1}X'Y \Rightarrow e \perp X \hat{\beta}$$

Proof  (T1)
First note that:

- $X'X\_{p \times p} \hat{\beta}\_{p \times 1}$
- $= X'X(X'X)^{-1}X'Y$
- $= X'Y$

Now observe that:

- $X'e = X'(Y - X \hat{\beta})$
- $= X'Y - X'X \hat{\beta}$
- $= X'Y - X'Y = \textbf{0}\_{p \times 1}$

We have established that $X'e = \textbf{0}\_{p \times 1}$

To complete the proof observe that:

 - $(X\hat{\beta})'e$
 - $= \hat{\beta}'\_{1 \times p}[X'\_{p \times n}e\_{n \times 1}]$ by (Transposition)
 - $= \hat{\beta}'\_{1 \times p} \textbf{0}\_{p \times 1} = 0$

 $$\hat{\beta} = (X'X)^{-1}X'Y \Rightarrow e \perp X \Rightarrow  e \perp X \hat{\beta}$$

### The Pythagorean Relation

(T2)  $$ \lVert Y \rVert^{2}  =  \lVert X\hat{\beta} \rVert^{2} + \lVert e \rVert^{2} $$

Proof (T2) The Pythagorean Relation Holds

- $\lVert X\hat{\beta} + e \rVert^{2}$
- $= (X\hat{\beta} + e)'(X\hat{\beta} + e)$
- $= X\hat{\beta}'X\hat{\beta} +X\hat{\beta}'e + e'X\hat{\beta} + e'e$
- $= (X\hat{\beta})'X\hat{\beta} + e'e$ By (Triangle) and (T1)
- $= \lVert X\hat{\beta} \rVert^{2}  + \lVert Y - X\hat{\beta} \rVert^{2}$
- $= \lVert X\hat{\beta} + Y - X\hat{\beta} \rVert^{2}$ By (Triangle)
- $= \lVert Y \rVert^2$

![OLS](/projects/OLS/OLSThreeDimensions.jpg)

### The Relation to Alternative Estimates

(T3) If $\bar{\beta}$ is $p \times 1$, then $\lVert Y - X\bar{\beta} \rVert^{2}  =  \lVert Y - X\hat{\beta} \rVert^{2} + \lVert X(\hat{\beta} - \bar{\beta}) \rVert^{2}$

Proof (T3)

- $\lVert Y - X \hat{\beta} \rVert^{2} +  \lVert X (\hat{\beta} - \bar{\beta}) \rVert^{2}$
- $=  \lVert e \rVert^{2} +  \lVert X (\hat{\beta} - \bar{\beta}) \rVert^{2}$
- $= \lVert e + X (\hat{\beta} - \bar{\beta}) \rVert^{2}$ since $e \perp X (\hat{\beta} - \bar{\beta})$
- $= \lVert (e + X \hat{\beta}) - X\bar{\beta}) \rVert^{2}$
- $= \lVert Y - X\bar{\beta}) \rVert^{2}$ by (T2)

### The Best Approximation Theorem

(T4) The Best Approximation Theorem

-  $(T3) \Rightarrow  min\lVert Y - X\bar{\beta}) \rVert^{2} = min\Big[\lVert Y - X\hat{\beta} \rVert^{2} + \lVert X(\hat{\beta} - \bar{\beta}) \rVert^{2}\Big]$

But then choosing $\bar{\beta} = \hat{\beta}$ gives us:

$$ min\Big[\lVert Y - X\hat{\beta} \rVert^{2} + \lVert \textbf{0} \rVert^{2}\Big]$$

Choosing either $\bar{\beta}  > \hat{\beta}$ or $\bar{\beta} < \hat{\beta}$ gives us:

$$ min\Big[\lVert Y - X\hat{\beta} \rVert^{2} + \lVert \pm \textbf{n} \rVert^{2}\Big]$$

### Closing the cirlce
$$ (T5): \forall \bar{\beta}_{p \times 1} [(Y - X\bar{\beta}) \perp X ] \Rightarrow \bar{\beta} = \hat{\beta} $$

- $(Y - X\bar{\beta}) \perp X$
- $\Rightarrow X'(Y - X\bar{\beta}) = 0$
- $\Rightarrow X'Y - X'X\bar{\beta} = 0$
- $\Rightarrow X'X\bar{\beta} = X'Y$
- $\Rightarrow \bar{\beta} = (X'X)^{-1}X'Y = \hat{\beta}$

$$ \hat{\beta} = (X'X)^{-1}X'Y  \Leftrightarrow e \perp X $$

### Covariance and Random Variables

$$cov(X) = E((X - \mu\_{X})(X - \mu\_{X})')$$

(L6) $$cov(AX) = Acov(X)A^{'} \text{ where X is random and A is fixed }$$

- $cov(AX)$
- $= E((AX - E(AX) (AX - E(AX)^{'}))$
- $= E( (AX - AE(X))(AX - AE(X))^{'})$ by Linearity of E
- $= E(A(X - E(X)) (A(X -E(X)))^{'})$
- $=  E(A(X - E(X))((X -E(X))'A'))$ by (L1)
- $= A cov(X)A'$

(L7) $$cov(U + u) = cov(U)$$ where u is a vector of constants and U is random.

- $cov(U + u)$
- $= E((U + u - E(U + u))(U + u - E(U + u))')$
- $= E((U + u - [E(U) + u]))(U + u - [E(U) + u])')$
- $= E((U - E(U) + u - u))(U - E(U) + u - u)')$
- $= cov(U)$

### Covariance and Random Variables

(L8) Assume $$ X \text{ is random }, c\_{p \times 1}, \text{ then } var(c' X) = c'cov(X)c $$

- $var(c'X) = E((c'X) - E(c'X)((c'X - E(c'X))')$
- $= E((c'X) - c'E(X)((c'X - c'E(X))')$
- $= E(c'X -  c'E(X))(c'X - c'E(X))'$
- $= c'E(X - E(X))(X' - E(X)')c)$
-$ = c'E(X - E(X))(X' - E)$
- $= c'cov(X)c$

### The Gauss Markov Theorem

![](/Users/nathanielforde/Desktop/Data Science/variance.jpg)
Assume that:

$$  (1) \, Y = (X \beta + \epsilon)\_{n \times 1} \qquad   (2) \,  E( \epsilon | X) = X^{'}\epsilon = 0\_{n \times 1} $$
and
$$(3) \, cov(\epsilon | X) =  \sigma^{2} I\_{n \times n}  \qquad (4) \, \epsilon \text{ is independent of } X $$

$\text{The Gauss Markov Theorem:}$ The OLS estimate is the best linear unbiased estimate. In the sense that the estimate has the least variance of all candidate unbiased linear estimates

By assumption (1) we have $Y  = X\beta + \epsilon$ so then

- $\hat{\beta} = (X^{'}X)^{-1}X^{'}Y$
- $= (X^{'}X)^{-1}X^{'}(X\beta + \epsilon)$
- $= (X^{'}X)^{-1}X^{'}X\beta + (X^{'}X)^{-1}X^{'}\epsilon$
- $= \beta + (X^{'}X)^{-1}X^{'}\epsilon$

Hence (T6): $\hat{\beta} \text{ is unbiased, } E(\hat{\beta} | X) = \beta$

- $E(\hat{\beta} | X)$
- $=  \beta + (X^{'}X)^{-1}X^{'}\epsilon$
- $= \beta + (X^{'}X)^{-1}E(\epsilon | X)$
- $= \beta$ by assumption (2)


(T7): $$ cov(\hat{\beta} | X)  =  \sigma^{2}(X'X)^{-1}$$

- $cov(\hat{\beta} | X)  =  cov(\beta + (X^{'}X)^{-1}X^{'}\epsilon)$
- $= cov((X^{'}X)^{-1}X^{'}\epsilon)$ by (L7)
- $= (X'X)^{-1}X'cov(\epsilon | X)X(X'X)^{-1}$ by (L6), (L1)
- $= (X'X)^{-1}X'\_{p \times n}\sigma^2I\_{n\times n}X(X'X)^{-1}$ by assumption (3)
- $= \sigma^2[(X'X)^{-1}X'X(X'X)^{-1}]$ since $\sigma^2$ is a scalar
- $= \sigma^2(X'X)^{-1}$


(L9): $$ \bar{\beta} = ((X^{'}X)^{-1}X^{'} + D\_{non-zero})Y \text{ is unbiased } \Rightarrow DX = 0$$

- $\Big( (X^{'}X)^{-1}X^{'} + D \Big) (X\beta + \epsilon)$
- $[(X^{'}X)^{-1}X^{'} + D]X\hat{\beta} + [(X^{'}X)^{-1}X^{'} + D]\epsilon$
- $[(X^{'}X)^{-1}X^{'}X\hat{\beta} + DX\hat{\beta} + [(X^{'}X)^{-1}X^{'} + D]\epsilon$
- $\Rightarrow E(\bar{\beta} | X) = \beta \Rightarrow DX = 0$


(T8) $$ \forall \bar{\beta}_{unbiased} \Big(cov(\bar{\beta}) = cov(\hat{\beta}) + \sigma^2DD' \Big) $$

- $cov(\bar{\beta}) = cov(((X^{'}X)^{-1}X^{'} + D_{non zero})Y)$
- $= ((X^{'}X)^{-1}X^{'} + D)cov(Y)((X^{'}X)^{-1}X^{'} + D)'$ by (L6)
- $= ((X^{'}X)^{-1}X^{'} + D) \sigma^2 ((X^{'}X)^{-1}X^{'} + D)'$ by assumption (1)
- $= \sigma^2((X^{'}X)^{-1}X^{'} + D) ((X^{'}X)^{-1}X^{'} + D)'$
- $= \sigma^2((X^{'}X)^{-1}X^{'} + D) (X(X^{'}X)^{-1} + D')$ by transposition
- $\sigma^2[(X'X)^{-1}X'X(X'X)^{-1} + DX(X'X)^{-1} + (X'X)^{-1}X'D' +DD']$
- $= \sigma^2[(X'X)^{-1} + DD'] = cov(\hat{\beta}) + \sigma^2DD'$ since $DX = 0$ by L8


 (L10)

$A\_{n \times p}A\_{p \times n}' \text{ is semi positive definite i.e for any suitable } x\_{n \times 1}$
$\text{we have } x'AA'x \geq 0$

Consider $A'x = y$

- $\Rightarrow x'AA'x = (A'x)'Ax = \lVert A'x \rVert^2 = \lVert y \rVert^2 \geq 0$
- $\Rightarrow \sigma^2DD' \text{ is semi positive definite }$

(GM): Let $\sigma^2DD'= DD'$

- $var(l'\bar{\beta}) = l'cov(\bar{\beta})l$ by (L8)
- $= l'[cov(\hat{\beta}) + D]l = l'cov(\hat{\beta})l + l'DD'l$ by (T8)
- $= \Big( var(l'\hat{\beta}) + \lVert D'l \rVert \Big) \geq var(l'\hat{\beta})$
