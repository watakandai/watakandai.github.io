---
title: "Optimization: Cheatsheet for myself"
categories:
  - optimization
tags:
  - "optimization"
use_math: true
published: true
---

# Definitions
## Objective
Let $x$ be a decision variable and $f(x)$ be an objective function.
$f(x): \mathbb{R}^n \to \mathbb{R} \quad (x\in \mathbb{R}^n)$. <br>

$$
\displaystyle{\min_{x \in \mathbb{R}^n} f(x)} \quad or \quad \displaystyle{\max_{x \in \mathbb{R}^n} f(x)}
$$

(For constraint problem: Let constraint region set be $F \subset \mathbb{R}^n$. Then $x \in F$. F is called a feasible region)

$$
\displaystyle{\min_{x \in F} f(x)} \quad or \quad \displaystyle{\max_{x \in F} f(x)} \quad (\text{s.t.} \ x \in F)
$$

- **Equality Constraints**: $h(x)=0$ where $h: \mathbb{R}^n \to \mathbb{R}^r$
- **Inequality Constraints**: $g(x)\leq 0$ where $g: \mathbb{R}^n \to \mathbb{R}^m$

Now a feasible region (set) $F$ can be defined as

$$
F = \{ x \in \mathbb{R}^n  \ | \ h(x)=0, \ g(x)\leq 0 \}
$$


## Optimality
Let $x^{\ast} \in \mathbb{R}^n$ is an optimal solution.

| | Local Optimal Solution | Global Optimal Solution |
|-|------------------------|-------------------------|
| Condition | $f(x^{\ast}) \leq f(x)$ | $f(x^{\ast}) \leq f(x)$ |
| Region | for $\epsilon >0$, $x\in F \cap B(x^{\ast}, \epsilon)$ where $B(x^{\ast}, \epsilon) = \{x \in \mathbb{R}^n: \| x - x^{\ast} \| < \epsilon \}$| $x, x^{\ast} \in F$ |

Local optimization finds an optimal solution within region where feasible reagion $F$ intersects with a bounded region $B(x^{\ast}, \epsilon)$. Global optimaztion finds an optimal solution just within a feasible region $F$.

Let $L(x, \lambda)$ be a Lagrange function.

$$
L(x, \lambda) = f(x) + \lambda h(x)
$$

No Constraint Optimization

| Condition | 1D | Vector |
|-----------|----|--------|
| 1st order Stationary | $\frac{\partial f}{\partial x}(x^{\ast})=0$ | $\nabla_x f(x^{\ast})=0$ |
| 2nd order Neccessary | $\frac{\partial f}{\partial x}(x^{\ast})=0$, $\frac{\partial^2 f}{\partial x^2}(x^{\ast})\geq 0$ | $\nabla_x f(x^{\ast})=0$, $t^T \nabla_x^2 f(x^{\ast})t\geq 0$ where $\forall t \in \mathbb{R}^n$ |
| 2nd order Sufficient | $\frac{\partial f}{\partial x}(x^{\ast})=0$, $\frac{\partial^2 f}{\partial x^2}(x^{\ast}) > 0$ | $\nabla_x f(x^{\ast})=0$, $t^T \nabla_x^2 f(x^{\ast})t> 0$ where $t \in \mathbb{R}^n \ \& \ t \neq 0$ |


With Constraints

| Condition | Equality Constraint | Inequality Constraint |
|-----------|---------------|-----------------|
| 1st order Stationary | $\nabla_x L(x^{\ast}, \lambda^{\ast})=0$ <br> $\nabla_{\lambda} L(x^{\ast}, \lambda^{\ast})=h(x^{\ast})=0$  where $\lambda^{\ast} \in \mathbb{R}^n$ | $\nabla_x L(x^{\ast}, \mu^{\ast})=0$ <br> $\mu_j^{\ast}g_j(x^{\ast})=0$ <br> $\mu_j^{\ast} \geq 0, \ g_j(x^{\ast})\leq 0$ where $(j=1, \dots, m)$|
| 2nd order Neccessary | $t^T \nabla_x^2 L(x^{\ast}, \lambda^{\ast})t\geq 0$ where $\forall t \in M(x^{\ast})$ | $t^T \nabla_x^2 L(x^{\ast}, \mu^{\ast})t\geq 0$ where $\forall t \in M(x^{\ast})$ |
| 2nd order Sufficient | $t^T \nabla_x^2 L(x^{\ast}, \lambda^{\ast})t> 0$ where $\forall t \in M(x^{\ast}) \ \& \ t \neq 0$ | $t^T \nabla_x^2 L(x^{\ast}, \mu^{\ast})t\geq 0$ where $\forall t \in M(x^{\ast}) \ \& \ t \neq 0$ |

where $M(x^{\ast})=\\{ t \in \mathbb{R}^n \| \nabla_x h_i(x^{\ast})^T t = 0, i=1, \dots, r \\}$ and $M(x^{\ast})=\\{ t \in \mathbb{R}^n \| \nabla_x g_j(x^{\ast})^T t = 0, j=1, \dots, m \\}$ respectively for equality and inequality constraints. $x$ is only considered in set $M$ because optimization should only be considered within the equality constraint.

> $\mu_j^{\ast} g_j(x^{\ast})=0$ is called a ***Complimentarity Condition**. When the solution is optimal $x^{\ast}$ this holds true, in other word, you can also try to minimize the complimentarity gap. The interior point method utilizes this characteristics to optimize a linear programming in a polynomial time.

> Linear independence constraint qualification:<br>
**$\nabla h_i(x)$ are linear independent, thus Jacobian $J_h(x)$ is a full rank matrix**. <br>
Similary, **$\nabla g_j(x)$ are also linear independent to each other**

<span style="color:red">TODO</span>: Why Linear Independence?

<span style="color:red">TODO</span>: Proof why $x^{\ast}$ is the local minimum

## Lagrangian
Why does it work?
1. Function $f(x)$ is stationary at Point P
  - $f(x)$ can be increased or decreased at Point Q even if $h(x)=0$ is satisfied
  - Even if you try to increase $f(x)$ by moving $x+\delta x$ at point P, it stays at Point P.
2. Thus, direction of $\frac{\partial h}{\partial x}$ should be aligned to $\frac{\partial f}{\partial x}$
3. $\frac{\partial h}{\partial x} = \kappa \frac{\partial f}{\partial x}$
4. Convert to $\frac{\partial f}{\partial x} + \lambda \frac{\partial h}{\partial x} = 0$
5. Then $L(x, \lambda) = f(x) + \lambda h(x)$

Here, the Lagrange multiplier quantifies how much it puts weight on the original cost (objective). As it gets small, it gets very close to the original problem. However, if it gets larger, it only cares about feasible region, where it gets close to an "analytic center" of a polyhedron.

> Vanderbei, Linear Programming, p.272 [1]<br>
We need to say how to pick μ. If μ is chosen to be too large, then the sequence
could converge to the analytic center of the feasible set, which is not our intention.
If, on the other hand, μ is chosen to be too small, then the sequence could stray too
far from the central path and the algorithm could jam into the boundary of the fea-
sible set at a place that is suboptimal.

<img src="https://user-images.githubusercontent.com/11141442/79500974-8a71a580-7fea-11ea-9990-77310b0d504d.png" width="400">[2]

## KKT Condition

|  | Equation |
|--|----------|
| Lagrange Function | $L(x, \lambda, \mu) = f(x) + \lambda h(x) + \mu g(x)$ |
| 1st Order Stationary Condition | $\nabla_x L(x^{\ast}, \lambda^{\ast}, \mu^{\ast}) = \nabla_x f(x^{\ast}) + \sum_i{\lambda_i \nabla_x h_i(x^{\ast})} + \sum_j{\mu_j \nabla_x g_j(x^{\ast})} = 0$ <br> $\nabla_{\lambda} L(x^{\ast}, \lambda^{\ast}, \mu^{\ast}) = h_i(x^{\ast}) = 0$ <br> $\mu_j^{\ast} g_j(x^{\ast}) = 0 \quad (j = 1, \dots, m)$ <br> $\mu_j^{\ast} \geq 0 \quad (j = 1, \dots, m$ <br> $g_j(x^{\ast}) \leq 0 \quad (j = 1, \dots, m)$ |
| 2nd Order Necessary Condition | $t^T \nabla_x^2 L(x^{\ast}, \lambda^{\ast}, \mu^{\ast}) t \geq 0$ <br> $\text{where} \quad \forall t \in M(x^{\ast})$ <br> $\text{where} \quad M(x) = \\{x \in \mathbb{R}^n \| \nabla_x g_j(x)^T t \leq 0 \quad (j=1,\dots,m), \\ \nabla_x h_i(x)^T t =0 \quad (i=1,\dots, r) \\}$ |
| 2nd Order Safficient Condition | $t^T \nabla_x^2 L(x^{\ast}, \lambda^{\ast}, \mu^{\ast}) t > 0$ <br> $\text{where} \quad \forall t \in M(x^{\ast}) \ \& \ t \neq 0$ |


## Convexity
#### Convex Set
Set of $S \in \mathbb{R}^n$ is a ***convex set*** when for any $x, y\in S$, the equation below always holds true.

$$
\alpha x + (1-\alpha) y \in S
$$

#### Convex Function
A function $f(x)$ is a ***convex function*** over a convex set $C \in \mathbb{R}^n$ when the equation below always holds true.

$$
f(\alpha x + (1-\alpha)y) \leq \alpha f(x) + (1-\alpha) f(y) \quad \forall{\alpha} \in [0, 1]
$$

A ***Strictly Convex Function*** is:

$$
f(\alpha x + (1-\alpha)y) < \alpha f(x) + (1-\alpha) f(y) \quad \forall{\alpha} \in (0, 1)
$$


# Linear Programming
## Simplex: $\mathcal{O}(n^2)$
1. Add slack variables to the original problem
2. Move slack variables to one side of the equation
3. Pivot variables until you get to reach a optimal solution.

| | Primal Problem | Dual Problem |
|-|----------------|--------------|
||$\max c^t x$ <br> $\text{s.t} \ Ax \leq b$ <br> $\quad x \geq 0$ | $\max b^t y$ <br> $\text{s.t} \ A^ty \geq c$ <br> $\quad y \geq 0$ |
|slack| $Ax + w = b$ <br> $x, w \geq 0$ | $A^t y + v = c$ <br> $y, v \geq 0$

Weak Duality: <br>

$$
c^t x \leq y^tAx \leq y^tb \\
\text{Thus} \quad c^tx \leq y^t b
$$

Strong Duality: <br>

$$
c^tx \leq y^tAx \\
(c^t - y^tA)x \leq 0 \\
v^t x \leq  0 = 0 \\
\ \\
y^t b \geq y^t Ax \\
y^t(b - Ax) \geq 0 \\
y^tw \geq 0 = 0
$$

Thus, $c^tx = y^t b$

>Proof: Vertex is a the intersection of n faces of the polyhedron. n of the inequality have to be satisfied. & It must be a part of the feasible region.

By setting n basics variables to 0 (let's say $\omega$s), then you get n inequalities. $Ax - b = 0$.
Every feasible dictionary is a vertex in polygon, since dictoinary never exists when it doesn't satisfy inequality constraints. Set all the basics variables to 0, it must be a feasible dictionary while satisfying the inequality constraints.


>Proof: Pivot moves to adjacent vertex.

1 Entry variable becomes a Basic variable, which means it is only changing only 1 face and leaving n-1 faces in common.


# Gradient Methods
Apply taylor expansion to function $f(x): \mathbb{R}^n \to \mathbb{R}$ where $x \in \mathbb{R}^n$.

$$
f(x_0 + \Delta x) = f(x_0) + \nabla_x f(x_0)\Delta x + o(\|\Delta x\|)
$$

Since $\nabla_x f(x_0)\Delta x$ is not 0 and we want this to be negative, setting $\Delta x$ as below is called ***Gradient Method***.

#### Gradient Descent

$$
\Delta x = - (\nabla_x f(x_0))^T \\
\nabla_x f(x_0)\Delta x = - \| \nabla_x f(x_0) \|^2 < 0
$$

#### Conjugate Gradient Method
However, this doesn't ensure that it is heading straight to the minimum point. The direction of the gradient is orthogonal to the tangential to the contour $f(x)$. If this contour is a sphere, the direction of the gradient is pointing directly to the minimum point. However, the contour could be ellipsoid. Thus, conjugate gradient method is proposed.

#### Newton's Method
Let $F(x) = \frac{\partial f}{\partial x}(x)$ and apply a taylor expansion around the gradient vector.

$$
F(x_0 + \Delta x) = F(x_0) + \frac{\partial F}{\partial x}(x_0) \Delta x +o(\| \Delta x\|) = 0
$$
Then

$$
\Delta x = - \Big( \frac{\partial F}{\partial x}(x_0) \Big)^{-1} F(x_0) \\
\Delta x = - \Big( \frac{\partial^2 f}{\partial x^2}(x_0) \Big)^{-1} \frac{\partial f}{\partial x}(x_0)^T
$$

#### Quasi-Newton's Method
Broyden-Fletcher-Goldfarb-Shanno (**BFGS**) Method.
TODO.

# Mathematical Definitions

Non-zero Integer Set: $\mathbb{Z}_{\geq 0}$ <br>
$a \leq x \leq b$: $[a,b]$ <br>
$a < x < b$: $(a, b)$ <br>
n dim real number vector: $\mathbb{R}^n$ <br>
n x m dim real number matrix: $\mathbb{R}^{n \times m}$ <br>
$x = [x_1, ..., x_n]^T \in \mathbb{R}^n$ <br>
positive semidefinite: $x^TAx \geq 0$<br>
positive definite: $x^TAx > 0$ if $x \neq 0$<br>
gradient vector: $\nabla_x f(x) = \frac{\partial f}{\partial x} = [\frac{\partial f}{\partial x_1}, .., \frac{\partial f}{\partial x_n}]$<br>
Hessian: $\nabla_x^2 f(x) = \frac{\partial^2f}{\partial x_i y_j}$ <br>
Jacobian: $f(x) = [f_1(x), ..., f_m(x)]$ and $x = [x_1, ..., x_n]$, then <br>

$$
J=\frac{\partial f}{\partial x}=
\begin{bmatrix}
\frac{\partial f_1}{\partial x_1} & \dots & \frac{\partial f_1}{\partial x_n} \\
\vdots &  & \vdots \\
\frac{\partial f_m}{\partial x_1} & \dots & \frac{\partial f_m}{\partial x_n}
\end{bmatrix}
$$

# Notes
Before going to the main topic / equation, it is important to define a region, a variable and a function.

ex.) <br>
- $x$ is a real number vector $x \in \mathbb{R}^n$
- $f(x)$ is a function that maps from $\mathbb{R}^n$ to $\mathbb{R}$, written as $f(x): \mathbb{R}^n \to \mathbb{R} \quad (x\in \mathbb{R}^n)$
- $F$ is a feasible region in a constraint problem. $F \in \mathbb{R}^n$.

# References
- [1] Vanderbei, Linear Programming, p.272 <br>
- [2] Toshiyuki Otsuka, "Nonlinear Optimal Control"
