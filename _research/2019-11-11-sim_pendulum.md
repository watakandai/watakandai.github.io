---
title: "Simulation of Inverted Pendulum using a simple neural network"
categories:
  - statistics
tags:
  - "path integration"
  - "Feynman diagram"
use_math: true
published: true
description: "Simulation of Inverted Pendulum using Neural Network."
img: "sim_pendulum_incase.png"
---

**[YouTube Link](https://www.youtube.com/watch?v=r7sOc22s1KA)**

[![sim_pendulum](../../images/sim_pendulum_incase.png)](https://www.youtube.com/watch?v=r7sOc22s1KA)

Simulation of Inverted Pendulum using Neural Network

Matlab Simulation represents the inverted pendulum placed on a linear guide with only a motor and an encoder attached. The difficulty of this problem is unstabilizing the already stabilized pendulum to bring its nose to the top and stabilize it again.

The neural network was used to decide on when to transition from the unstabilized controller to the stabilized controller.

There is no teacher to this neural network. It learns by itself efficiently using Genetic Algorithm.
