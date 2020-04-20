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

