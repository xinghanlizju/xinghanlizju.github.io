---
title: How to deal with outliers in MLE
tags: [SLAM, statistics]
categories: working
mathjax: true
date: 2024-06-03 14:15:47
---

# How to deal with outliers in Maximum Likelihood Estimation problem
This post will introduce a common method for dealing with outliers in MLE problem.

## Intro
Consider a set of 3D points, $X={x_1,x_2,...,x_n}$ with $x_n\in\mathbb{R}^3$ on an arbitrary probability distribution. We want to estimate the parameter of a modelled probabiliry distribution based on Maximum Likelihood.

We can solve this numerically, i.e., use an iterative gradient descent method to minimize the negative log likelihood:
$$
\mathcal{L}=\sum_{x\in X}-\log(P(x|\theta))
$$
where $P(x|\theta)$ is the probability density function and $\theta$ is the vector of parameters to optimize.

## Method
We assume that the outliers satisfy a uniform PDF with constant denoted by $\Omega$. MLE without outlies can be written as:
$$
\hat{\theta} = \min_\theta \sum_{x \in X} -\log(P(x \, | \,\theta)).
$$
When considering the outliers, we have a new objective function
$$
\hat{\theta} = \min_\theta \sum_{x \in X} -\log(w_1 \, P(x \, | \,\theta) + w_2 \, \Omega),
$$
using $w_2=1-w_1$.
Reranging terms, we have the following equivalent form:
$$ \hat{\theta} =  \min_\theta  \sum_{x \in X} -\log( \, P(x \, | \,\theta) + \frac{1-w_1}{w_1} \, \Omega -\log(w_1).
$$

If you know more properties about outliers, and if you know more about the true distribution (e.g. potential correlation between measurements), then you can incorporate this into the likelihood function.