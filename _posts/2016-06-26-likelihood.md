---
title: "Maximum likelihood and KL divergence"
categories:
  - statistics
tags:
  - mle
  - maxent
use_math: true
published: true
---

In this post I describe some of the theory of maximum likelihood estimation (MLE), highlighting its relation to information theory. In a later post I will develop the theory of maximum entropy models, also drawing connections to information theory, hoping to clarify the relation between MLE and MaxEnt. This serves as only a brief introduction that is relatively basic. That said, some prior familiarity with probability and statistics is of course assumed.

Maximum likelihood has been developed and advocated by many figures throughout the history of mathematics. (See [1] for a nice overview.) In a sense it was first considered in a significant way by Lagrange, but it was also considered by Bernoulli, Laplace, and Gauss, among others. Indeed, what was known as the 'Gaussian method' involved maximum aposteriori estimation of a model with normal distributed errors and a uniform prior, resulting in what is now known as the method of least squares. However, its theory and use was advanced most strongly by Fisher in the 1920s and 30s. Fisher worked for many years to demonstrate conditions sufficient for both the consistency of MLE and for a property known as efficiency. While his later results have stood up to scrutiny, the theory, as it stands, does not possess the generality one might hope for. Nonetheless, it remains a cornerstone of contemporary statistics.

## Maximum likelihood estimation

Much of statistics relies on identifying models of data that are, in some sense, close to our observations. Indeed, in many cases it seems sensible that we seek models that are the closest to our observations. Maximum likelihood provides one principle by which we may identify these  closest distributions. It has many appealing properties that make it an appropriate measure, and is a broadly applicable method. As we will see, its simplicity hides many treasures.

The theory is easiest to describe in a discrete setting, which we will address first. Let

$$
  {x} = (x_1, x_2, \cdots, x_N)
$$

describe $N$ observations drawn from a discrete probability distribution. Each draw $x_n\in\mathcal{X}$ is taken from an alphabet of $M$ characters, $\mathcal{X}=(a_1, \dots, a_M)$. Let $p_m$ denote the probability of drawing character $m$ in any one draw, and let $f_m$ denote the frequency of character $m$ is observed in the $N$ draws. Note that we're just describing a multinomial distribution having $M$ parameters $p_m$.

Given our observations, how should we estimate the multinomial parameters $\mathbf{p}$? The principle of maximum likelihood states simply that we take parameters that result in our observations having highest probability, when compared with all other possible choices of parameters. If we assume that each draw is independently and identically distributed (i.i.d.) then this is

  $$
  \begin{align}
    a & = asdf \\\\
    & asdf
  \end{align}
  $$

Hi there asdf

$$
\begin{align}
\hat{\mathbf{p}}_{MLE} &amp; = \text{argmax}_{p\in \mathcal{P}} \prod_{n=1}^Np_{x_n}\\\\
&amp; = \text{argmax}_{p\in\mathcal{P}}\log\left(\prod_{n=1}^Np_{x_n} \right) \\\\
&amp; =\text{argmax}_{p\in\mathcal{P}}\sum_{n=1}^N\log \left( p_{x_n} \right) \\\\
&amp; =\text{argmax}_{p\in\mathcal{P}}\sum_{m=1}^M f_m \log \left( p_m \right) 
\end{align}
$$

For many reasons, some of which will become clear here, expressing the maximization problem in terms of logarithms is the natural choice, so the last line above is one we will be optimizing. (As a brief aside, note the step taken to reach the last line appears a trivial manipulation, but if we were to write out what was happening in a general probability space, it is roughly analogous to the change of variables:

$$
\begin{align*}
\int_\mathbb{R} \log (p(x)) dF(x) = \int_\Omega \log(p(X(\omega)) d\mu(\omega)\end{align*}
$$

we make when shifting between expectations in terms of a measure $\mu$ and a distribution function $F$. The LHS being given by a Riemann-Stieltjes integral, the RHS by a Lebesgue integral.)

The problem is constrained by the fact that $\sum q_m = 1$ and $q_m\ge 0 \forall m$. This constrained optimization problem  can be solved using Lagrange multipliers. Recall this involves augmenting our objective function with our constraints

$$
\begin{align*}\text{argmax}_{p\in\mathcal{P}} \sum_{m=1}^M\log \left(p_{x_n}\right) - \lambda (\sum_{m=1}^Mq_m - 1) + \sum_{m=1}^M\mu_m p_m\\
=\text{argmax}_{p\in\mathcal{P}} \mathcal{\tilde{L}}(\mathbf{p}, \mathbf{f})\end{align*}
$$

We set the partial derivative of the Lagrangian $\mathcal{\tilde{L}}$ taken with respect to $p_m$ to zero to obtain

$$
p_m = \frac{1}{\lambda + \mu_m} f_m
$$

It's useful to consider the empirical distribution:

$$
\hat{q}_m = \frac{\sum_{n=1}^N\mathbf{I}}{\|\mathbf{f}\|_1} = \frac{f_m}{N}
$$

## KL-divergence

We find that the optimal occurs at, not surprisingly, the empirical estimates for the mean. Thus we have that the MLE estimates for our multinomial distribution are maximized at the empirical distribution. I mentioned earlier that we would like some measure of closeness, and would like to find distributions that are 'close' our observations. What measure of closeness have we just minimized? In the discrete case the answer requires no measure theory, so we can state it right now. In the continuous case we need to be more careful.

We have just minimized the relative entropy, or Kullback-Liebler divergence:

$$
\begin{align*}
\hat{\mathbf{p}}_{MLE} = \text{argmin}_{\mathbf{p}\in\mathcal{P}} D_{KL}( \mathbf{p} | \mathbf{f}) \\
=\text{argmin}_{\mathbf{p}\in\mathcal{P}} \sum_{m=1}^M f_m \log \left( \frac{f_m}{p_m} \right) \\
=\text{argmin}_{\mathbf{p}\in\mathcal{P}} \sum_{m=1}^M f_m \log \left( f_m \right) - f_m \log \left( {p_m} \right) \\
=\text{argmax}_{\mathbf{p}\in\mathcal{P}} \sum_{m=1}^M f_m \log \left( {p_m} \right)
\end{align*}
$$

where we have taken as convention $0\log(0) = 0$.

We have discussed only the case where $\mathbf{p}$ is estimated non-parameterically. What if we instead have some probability model described by a parameter $\theta$? The result that maximum likelihood corresponds to minimizing the KL divergence remains. The optimization problem now becomes

$$
\begin{align}\hat{\theta}_{MLE} = \text{argmax}_{\theta\in\Theta}\mathcal{L}(\theta|\mathbf{f})\\
= \text{argmax}_{\theta\in\Theta}\sum_{m=1}^M f_m \log \left(p_m(\theta)\right)\\\end{align}
$$

which is typically found by solving $\mathcal{L}(\theta\|\mathbf{f}) = 0$.

## Consistency

Note that our result applies to the empirical distribution $\mathbf{f}$, i.e. for a finite amount of data. We need to ask what happens as we take an indefinite number of samples, does our non-parametric estimate $\mathbf{p}$ tend towards the true parameters $\mathbf{p}$, and does our parametric estimate $\hat{\theta}$ tend towards the true parameter $\theta$?

## Asymptotic efficiency

Arriving at equivalent results for general, continuous, distributions will be the subject of my next post.

## References:

[1] "The Epic Story of Maximum Likelihood" Stephen M. Stigler. 2007.
