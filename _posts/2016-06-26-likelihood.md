---
title: "Maximum likelihood and KL divergence"
categories:
  - statistics
tags:
  - mle
use_math: true
published: true
---

In this post I describe some of the theory of maximum likelihood estimation (MLE), highlighting its relation to information theory. In a later post I will develop the theory of maximum entropy models, also drawing connections to information theory, hoping to clarify the relation between MLE and MaxEnt. 

Maximum likelihood was developed and advocated by many figures throughout the history of mathematics (see [1] for a nice overview). In a sense it was first considered in a significant way by Lagrange, but it was also considered by Bernoulli, Laplace, and Gauss, among others. Indeed, what was known as the 'Gaussian method' involved maximum aposteriori estimation of a model with normal distributed errors and a uniform prior, resulting in what is now known as the method of least squares. However, its theory and use was advanced most strongly by Fisher in the 1920s and 30s. Fisher worked for many years to demonstrate conditions needed for both the consistency of MLE and efficiency. While his later results have stood up to scrutiny, the theory, as it stands, does not possess the quite generality he sought after. Nonetheless, it remains a cornerstone of contemporary statistics.

## Maximum likelihood estimation

Much of statistics relies on identifying models of data that are, in some sense, close to our observations. Indeed, in many cases it seems sensible that we seek models that are the closest to our observations. Maximum likelihood provides one principle by which we may identify theseclosest distributions. It has many appealing properties that make it an appropriate measure, and is a broadly applicable method. As we will see its simplicity is somewhat deceiving.

The theory is easiest to describe in a discrete setting, which we will address first. Let

$$
x = (x_1, x_2, \cdots, x_N)
$$

describe $N$ observations drawn from a discrete probability distribution. Each draw $x_n\in\mathcal{X}$ is taken from an alphabet of $M$ characters, $\mathcal{X}=(a_1, \dots, a_M)$. Let $p_m$ denote the probability of drawing character $m$ in any one draw, and let $f_m$ denote the frequency of character $m$ is observed in the $N$ draws. Note that we're just describing a multinomial distribution having $M$ parameters $p_m$.

Given our observations, how should we estimate the multinomial parameters $\mathbf{p}$? The principle of maximum likelihood states simply that we take parameters that result in our observations having highest probability, when compared with all other possible choices of parameters. If we assume that each draw is independently and identically distributed (i.i.d.) then this is

<div>
$$
\begin{align}
\hat{\mathbf{p}}_{MLE} &amp; = \text{argmax}_{p\in \mathcal{P}} \prod_{n=1}^Np_{x_n}\\
&amp; = \text{argmax}_{p\in\mathcal{P}}\log\left(\prod_{n=1}^Np_{x_n} \right) \\
&amp; =\text{argmax}_{p\in\mathcal{P}}\sum_{n=1}^N\log \left( p_{x_n} \right) \\
&amp; =\text{argmax}_{p\in\mathcal{P}}\sum_{m=1}^M f_m \log \left( p_m \right) 
\end{align}
$$
</div>

For many reasons, some of which will become clear here, expressing the maximization problem in terms of logarithms is the natural choice, so the last line above is one we will be optimizing. (As a brief aside, note the step taken to reach the last line appears a trivial manipulation, but if we were to write out what was happening in a general probability space, it is roughly analogous to the pushforward change of variables:

$$
\begin{align*}
\int_\mathbb{R} \log (p(x)) dF(x) = \int_\Omega \log(p(X(\omega)) d\mu(\omega)\end{align*}
$$

we make when shifting between expectations in terms of a measure $\mu$ and a distribution function $F$. The LHS being given by a Lebesgue-Stieltjes integral, the RHS by a Lebesgue integral.)

The problem is constrained by the fact that $\sum q_m = 1$ and $q_m\ge 0 \forall m$. This constrained optimization problem can be solved using Lagrange multipliers. Recall this involves augmenting our objective function with our constraints

$$
\text{argmax}_{p\in\mathcal{P}} \sum_{m=1}^M f_m \log \left(p_{x_n}\right) - \lambda (\sum_{m=1}^Mq_m - 1) + \sum_{m=1}^M\mu_m p_m = \text{argmax}_{p\in\mathcal{P}} \mathcal{\tilde{L}}(\mathbf{p}, \mathbf{f})
$$

We set the partial derivative of the Lagrangian $\mathcal{\tilde{L}}$ taken with respect to $p_m$ to zero to obtain

$$
p_m = \frac{1}{\lambda + \mu_m} f_m
$$

We find that the optimal occurs at, not surprisingly, the empirical estimates for the mean. Thus we have that the MLE estimates for our multinomial distribution are maximized at the empirical distribution. 

## KL-divergence

I mentioned earlier that we would like some measure of closeness, and would like to find distributions that are 'close' our observations. What measure of closeness have we just minimized? In the discrete case, we have just minimized the relative entropy, or Kullback-Liebler divergence between the empirical distribution and the model.

Recall the empirical distribution is

$$
\hat{q}_m = \frac{\sum_{n=1}^N\mathbf{I}}{\|\mathbf{f}\|_1} = \frac{f_m}{N}
$$

so that:

<div>
$$
\begin{align*}
\hat{\mathbf{p}}_{MLE} 
& = \text{argmax}_{\mathbf{p}\in\mathcal{P}} \sum_{m=1}^M f_m \log \left( {p_m} \right) \\
& = \text{argmin}_{\mathbf{p}\in\mathcal{P}} \sum_{m=1}^M \hat{q}_m \log \left( \hat{q}_m \right) - \hat{q}_m \log \left( {p_m} \right) \\
& = \text{argmin}_{\mathbf{p}\in\mathcal{P}} \sum_{m=1}^M \hat{q}_m \log \left( \frac{\hat{q}_m}{p_m} \right) \\
& = \text{argmin}_{\mathbf{p}\in\mathcal{P}} D_{KL}( \mathbf{q} | \mathbf{p}) \\
\end{align*}
$$
</div>

where we have taken as convention $0\log(0) = 0$. _Thus in the discrete case, at least, maximizing likelihood corresponds to minimizing the KL divergence._

We have discussed only the case where $\mathbf{p}$ is estimated non-parameterically. What if we instead have some probability model described by a parameter $\theta$? The optimization problem now becomes

<div>
$$
\begin{align}\hat{\theta}_{MLE} & = \text{argmax}_{\theta\in\Theta}\mathcal{L}(\theta|\mathbf{f})\\
& = \text{argmax}_{\theta\in\Theta}\sum_{m=1}^M f_m \log \left(p_m(\theta)\right)\\\end{align}
$$
</div>

which is typically found by solving $\frac{\partial}{\partial \theta}\mathcal{L}(\theta\|\mathbf{f}) = 0$. The result is the same however -- we minimize the difference between the empirical distribution and the parametric model. 

In the continuous case things are not so straightforward. We try the same argument for a real-valued random variable, $N$ observations $X^n = \{x_1, \dots, x_N \}$, and a parametric model $f(x; \theta)$. If we denote by

$$
p_D(x) = \frac{1}{N}\sum_{i=1}^{N}\delta(x-x_i)
$$

the 'empirical density', then trying the same argument gives:

<div>
$$
\begin{align*}
\hat{\theta}_{MLE} 
& = \text{argmax}_{\theta} \sum_{i=1}^N \log \left( f(x_i;\theta) \right) \\
\text{(??)} \qquad & = \text{argmax}_{\theta} \int p_D(x) \log \left( {f(x;\theta)} \right) \,dx\\
\text{(??)} \qquad & = \text{argmin}_{\theta} \int p_D(x) \log \left( \frac{p_D(x)}{f(x;\theta)} \right) \,dx\\
& = \text{argmin}_{\theta} D_{KL}( p_D | f(\dot; \theta)) \\
\end{align*}
$$
</div>

However, the question marked lines don't quite make sense -- it's unclear what the $log p_D(x)$ is in a continuous setting. For such a line to make sense the empirical distribution would have to be absolutely continuous so that $p_D$ was actually a density. 

## Consistency

That said, the intuition about MLE and min. $D_{KL}$ does carry through to the continuous setting in the following sense. Let the data be generated by a model given by $f(x; \theta^*)$ and let

$$
M_N(\theta) = \frac{1}{N}\sum_{i=1}^N \log(f(x_i;\theta))
$$

denote the quantity we maximize under MLE. We note that this is an approximation to the expectation

$$
M_N(\theta) \approx \mathbb{E}_{\theta^*} \log(f(X;\theta))
$$

and that, through the law of large numbers, this indeed converges to

$$
\lim_{N\to\infty} M_N(\theta) =M(\theta) = \mathbb{E}_{\theta^*} \log(f(X;\theta)).
$$

But maximizing $M(\theta)$ is the same as minimizing $D_{KL}(f(\theta^*)\|f(\theta))$ since:

<div>
$$
\max_{\theta} M(\theta) = \max_{\theta} \mathbb{E}_{\theta^*} \log \left(\frac{f(X;\theta)}{f(X;\theta^*)}\right) = \min_{\theta} D_{KL}(f(\theta^*)\|f(\theta))
$$
</div>

Note that since $D_{KL} \ge 0$ and $D_{KL}(f\|g) = 0 \iff f = g$ then, under regularity conditions not specified here, this result implies the consistency of the MLE -- that $\theta_N \to^{P} \theta^*$ as $N\to\infty$. 

## Some useful references

1. "The Epic Story of Maximum Likelihood" Stephen M. Stigler. 2007.
2. https://terrytao.wordpress.com/2015/09/29/275a-notes-0-foundations-of-probability-theory/
3. https://terrytao.wordpress.com/2015/10/03/275a-notes-1-integration-and-expectation/

Some blogs posts covering similar material:

* http://www.hongliangjie.com/2012/07/12/maximum-likelihood-as-minimize-kl-divergence/
* https://quantivity.wordpress.com/2011/05/23/why-minimize-negative-log-likelihood/
