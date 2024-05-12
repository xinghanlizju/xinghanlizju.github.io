---
title: schur-complement BA vs MSCKF
tags: SLAM
---

# 什么是Schur-complement BA和MSCKF？
## Schur-complememt BA 是一种有效解决非线性最小二乘问题的方法
SLAM 问题可以列成一系列优化变量（Bundle adjustment）的非线性最小二乘问题, 其形式可以写成如下：
$$\argmin_{x\in\mathcal{X}}\|r(x)\|^2,$$
其中$\mathcal{X}$是变量的集合，包括相机位置姿态以及landmark的位置信息。

这类优化问题SLAM里面通常的解法是[Gaussian-Newton](https://en.wikipedia.org/wiki/Gauss%E2%80%93Newton_algorithm#)方法以及[Levenberg–Marquardt algorithm](https://en.wikipedia.org/wiki/Levenberg%E2%80%93Marquardt_algorithm)。这类数值求解方法从效率和准确性上对于SLAM大规模优化变量以及非线性优化问题都没有充分利用。Schur补就是这样的一种变种用来针对每次迭代过程中进行效率的提升。

下面给出Schur-complement的定理：
```{theorem, label, name="Schur-complement"}
Here is my theorem.
```