---
title: "[Cheatsheet] Optimal Control"
categories:
  -optimal control
tags:
  - "optimal control"
use_math: true
published: true
---

# Optimal Control
- Formulation
Let model be a function $x(k+1) = f(x(k), u(k), k): \mathbb{R}^n \times \mathbb{R}^m \times \mathbb{Z}_{\geq 0} \to \mathbb{R}^n$ where $x \in \mathbb{R}^n$

| Name | Equation |
|------|----------|
| Objective Function | $J(x) = \varphi(x(N)) + \sum_{k=0}^{N-1} L(x(k), u(k), k)$ |
| Lagrange | $\overline{J} = J + \sum_{k=0}^{N-1} \lambda(k+1) (f(x,u,k) - x(k+1)$ |
| Hamiltonian | $H(x,u,\lambda,k)=L(x,u,k) + \lambda^T f(x,u,k)$ |



- Ricatti Equation
- LQ
- MPC

$$
\begin{align}
    x_{2} &= Ax_1 + Bu_1 + B_dd_1 \nonumber\\
    &=A(Ax_0+Bu_0+B_dd_0) + Bu_1 + B_dd_1 \nonumber\\
    &=A^2x_0+ABu_0+AB_dd_0+Bu_1+B_dd_1 \nonumber\\
    &=A^2x_0+ \nonumber

    \begin{bmatrix}
    AB&B
    \end{bmatrix}
    \begin{bmatrix}
    u_0\\
    u_1
    \end{bmatrix}
    +
    \begin{bmatrix}
    AB_d&B_d
    \end{bmatrix}
    \begin{bmatrix}
    d_0\\
    d_1
    \end{bmatrix}\nonumber
\end{align}
$$

$$
\begin{align}
J(U)=& (H(Fx_0+GU+SD)-Y_{ref})^TQ(H(Fx_0+GU+SD)-Y_{ref}) \nonumber \\
&+(U-U_{ref})^TR(U-U_{ref}) \nonumber \\
=&U^T(G^TH^TQHG+R)U+2\{(H(Fx_0+SD)-Y_{ref})^TQHG-U_{ref}^TR\}U \nonumber\\
&+ const \nonumber
\end{align}
$$
