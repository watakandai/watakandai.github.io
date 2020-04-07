---
title: "Causal inference algorithms for learning in neural networks"
categories:
  - neuroscience
use_math: false
published: true
---

A number of challenges in both machine learning (ML) and neuroscience are related to causation. In ML, causality relates to issues of transfer and generalization, fairness, and safety; and in neuroscience it relates to issues of interpretability and models of efficient learning. It can thus be beneficial to explicitly cast problems in both machine learning and neuroscience as causal inference problems. In [this](https://benlansdell.github.io/facultytalk/#/) presentation I explore this idea in the context of learning in neural networks. I show that framing the gradient estimation problem as one of causal inference can lead to new learning algorithms in spiking neural networks. These algorithms rely on, rather than smooth out, the spiking discontinuity. I then show how such causal effect estimators can be used to train weights of a feedback network to communicate gradient signals in a way that avoids the biologically implausible elements of the back-propagation algorithm. The result is learning algorithms with comparable performance to back-propagation, and better performance than other biologically plausible gradient-based learning rules, on simple benchmarks. These approaches thus yield efficient and plausible learning algorithms for the brain, which also have applications in neuromorphic hardware and specialized hardware optimized for implementing back-propagation.

[Slides](https://benlansdell.github.io/facultytalk/#/)

