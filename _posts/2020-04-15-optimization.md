---
title: "Optimization"
categories:
  - optimization
tags:
  - "optimization"
use_math: true
published: false
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
| Region | for $\epsilon >0$, $x\in S \cap B(x^{\ast}, \epsilon)$ | $x, x^{\ast} \in S$ |

where $B(x^{\ast}, \epsilon) = \{x \in \mathbb{R}: \| x - x^{\ast} \| < \epsilon \}$

| | No Constraint | With Constraint |
|-|---------------|-----------------|
| stationary condition | $\frac{\partial f}{\partial x}(x^{\ast})=0$ | aaa |
| 2nd order neccessary solution | $\frac{\partial f}{\partial x}(x^{\ast})=0$, $\frac{\partial^2 f}{\partial x^2}(x^{\ast})\geq 0$| aaa |
| 2nd ordre sufficient solution | $\frac{\partial f}{\partial x}(x^{\ast})=0$, $\frac{\partial^2 f}{\partial x^2}(x^{\ast}) > 0$ | aaa |


質問
- 凸 / 非凸 の定義
- 制約あり・なし
- 制約あり・なしでも凸？
- 最適解とは？ 局所的・大域的最適解
- 停留条件・２次の必要条件・２次の十分条件
- 相補性条件とは。成り立つ条件
- KKT Condition  $\frac{\partial L}{\partial x}=0$, $g\leq 0$, $h=0$, etc...
  - なぜ１次独立
- Lagrangeはなぜ成り立つ
- ラグランジュ乗数の意味
- 最適制御の定式化
- リカッチ方程式の導出方法３つ？
- 勾配法・共役勾配法（違いは？）・最急降下法・ニュートン法
- 凸計画問題 (+線形計画問題・２次計画問題)との違い
- シンプレックス法、内点法、楕円法、切除平面法
- シンプレックス法
  - Duality 双対性
  - PolyhedronのEdgeの意味. なぜエッジを伝って最適化していると言えるか
  - 過去の宿題の証明＋授業のノート


### Definition of Convexity
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


## Mathematical Definitions

Non-zero Integer Set: $\mathbb{Z}_{\geq 0}$ <br>
$a \leq x \leq b$: $[a,b]$ <br>
$a < x < b$: $(a, b)$ <br>
n dim real number vector: $\mathbb{R}^n$ <br>
n x m dim real number matrix: $\mathbb{R}^{n \times m}$ <br>
$x = [x_1, ..., x_n]^T \in \mathbb{R}^n$ <br>
positive semidefinite: $x^TAx \geq 0$<br>
positive definite: $x^TAx > 0$ if $x \neq 0$<br>
gradient vector: $\frac{\partial f}{\partial x} = [\frac{\partial f}{\partial x_1}, .., \frac{\partial f}{\partial x_n}]$<br>
Hessian: $\frac{\partial^2f}{\partial x_i y_j}$ <br>
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
