---
layout:     post
title:      FISTA
date:       2023-05-07 12:31:47
author:     Yifan Kang
visible:    true
---

*"Fast iterative shrinkage-thresholding algorithm"(FISTA)* is a provimal gradient method that aims to slove convex optimization problems of the form:

$$\min_x f(x) = g(x) + h(x)$$

where $g$ is a smooth convex function with a Lipschitz continuous gradient, and $h$ is a convex function that is possibly non-smooth but has a simple proximal operator.

## Proximal Gradient & ISTA

Let us start from the classical *proximal gradient* method (also known as *ISTA*), based on which the FISTA algorithm is built.

For a given convex optimization problem
$$\min_x f(x) = g(x) + h(x)$$
where $g$ is differentiable and $h$ is not nessarily differentiable, proximal gradient method apply gradient descent on $g$. Its update goes in the following fashion:

$$\begin{aligned} x^{+} & =\underset{z}{\operatorname{argmin}}~\bar{g}_t(z)+h(z) \\ & =\underset{z}{\operatorname{argmin}}~g(x)+\nabla g(x)^T(z-x)+\frac{1}{2 t}\|z-x\|_2^2+h(z) \\ & =\underset{z}{\operatorname{argmin}}~\frac{1}{2 t}\|z-(x-t \nabla g(x))\|_2^2+h(z)\end{aligned}$$

This can be written as a *proximal mapping*:

$$\operatorname{prox}_{h, t}(x)=\underset{z}{\operatorname{argmin}}~ \frac{1}{2 t}\|x-z\|_2^2+h(z)$$

Then the proximal gradient update can be written as
$$
x^{(k)}=\operatorname{prox}_{h, t_k}\left(x^{(k-1)}-t_k \nabla g\left(x^{(k-1)}\right)\right)
$$

or similar to gradient descent:

$$
x^{(k)}=x^{(k-1)}-t_k \cdot G_{t_k}\left(x^{(k-1)}\right)
$$
where $G_t(x)=\frac{x-\operatorname{prox}_{h, t}(x-t \nabla g(x))}{t}$ is the generalized gradient of $f$.

For many important functions $h$, for example, $l_1$-norm, there are closed-form proximal mapping $\operatorname{prox}_{h, t}$.

## FISTA

For a convex optimization problem:

$$\min_x f(x) = g(x) + h(x)$$

where $\nabla g$ is L-Lipschit, FISTA can be described as follows:

> - Step 0: 
>   - Take $y_1=x_0 \in \mathbb{R}^n, t_1=1$.
> - Step $k(k \geq 1)$:
>   - Compute
>       - $x_k=p_L\left(y_k\right)$
>       - $t_{k+1}  =\frac{1+\sqrt{1+4 t_k^2}}{2}$
>       - $y_{k+1}  =x_k+\left(\frac{t_k-1}{t_{k+1}}\right)\left(x_k-x_{k-1}\right)$

The step size can also be determined using a backtracking rule:

> - Step 0: 
>   - Take $y_1=x_0 \in \mathbb{R}^n, t_1=1$, $\eta >1$, $L_0>0$.
> - Step $k(k \geq 1)$:
>   - Find the smallest nonnegative integers $i_k$ such that with $\bar{L}=\eta^{i_k} L_{k-1}$,
>        - $F\left(p_{\bar{L}}\left(y_k\right)\right) \leq Q_{\bar{L}}\left(p_{\bar{L}}\left(y_k\right), y_k\right) $
>   - Set $L_k=\eta^{i_k} L_{k-1}$ and compute
>        - $x_k  =p_{L_k}\left(y_k\right)$
>        - $t_{k+1}  =\frac{1+\sqrt{1+4 t_k^2}}{2}$
>        - $y_{k+1}  =x_k+\left(\frac{t_k-1}{t_{k+1}}\right)\left(x_k-x_{k-1}\right)$

FISTA with fixed step size $t \leq 1 / L$ satisfies
$$
f\left(x^{(k)}\right)-f^{\star} \leq \frac{2\left\|x^{(0)}-x^{\star}\right\|_2^2}{t(k+1)^2}
$$
and same result holds for backtracking, with $t$ replaced by $\beta / L$. This means that FISTA achieves an optimal rate of $O(\frac{1}{k^2})$ or $O(\frac{1}{\sqrt \epsilon})$.