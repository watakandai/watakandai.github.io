---
title: "Relay Policy Learning: Solving Long-Horizon Tasks via Imitation and Reinforcement Learning"
categories:
  - rl
tags:
  - "reinforcement learning"
  - "hierarchical"
use_math: true
published: true
---

[PDF](https://arxiv.org/abs/1910.11956)

####  Abstract
Proposed a hierarchical reinforcement learning method that learns atomic actions via imitation learning and fin-tuned via reinforcement learning.
Simplify long-horizon policy learning problem. How? Proposed a novel "data-relabeling algorithm" for learning a goal-conditioned hierachical policies.

No access to specific task, leverage unstructured & unsegmented demonstrations for imitation learning.

#### Why is it good?
Recent RL are constrained to relatively simple short-horizon skilss, HRL is used instead.
However, HRL struggle with exploration(h-DQN), skill segmentation(option), and reward definition(Diversity is all you need).
We simplify problem by utilizing extra supervision in the form of unstructured human demonstrations.

#### Core Technology
- Goal-conditioned RL that learns $pi(a|s,sg)$ such that it maximizes goal-conditioned expected reward $E_{sg~G}[E_pi[\Sigma_{t=0}^T gamma^t r_i(st, at, sg)]]$.
and Imitation Learning that learns $pi(a,s,sg)$ such that it maximizes $E_{(s,a)~D}[log pi(a|s)]$. (See Learning to achieve goals)
- Relay Policy Architecture
- Relay Data Relabeling which constructs a low-level dataset $D_l$ and a high-level dataset $D_h$ from these demonstration
Make 1~W_l steps away samples as goal for the low level learner, Make1~W_h steps away samples as a gaol for the high level learner.  (See  Learning latent plans from play by Lynch & Sergey)
Higher level learner takes actions $s_{t+min(w, W_l)}$ which aims to set a sufficiently distant subgoal as a the high-level action.
- The objective function is to maximize the likelihood of the actions taken given the corresponding states and goals.
Combinining the imitation learning objectives and RL objectives, the gradient of the objective is the combined natural gradient of the objective functions.

#### Verification Method

#### Any Argument or Idea?

#### Next Paper to read

# Details
