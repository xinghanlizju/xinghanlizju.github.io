---
title: schur-complement BA
tags: SLAM
date: 2024-05-12 22:14:38
mathjax: true
---


# 什么是Schur-complement BA？
## SLAM问题背景
SLAM 问题可以列成一系列优化变量（Bundle adjustment）的非线性最小二乘问题, 其形式可以写成如下：
$$\text{argmin}_{x\in\mathcal{X}}\|r(x)\|^2,$$
其中$\mathcal{X}$是变量的集合，包括相机位置姿态以及landmark的位置信息。

这类优化问题SLAM里面通常的解法是[Gaussian-Newton](https://en.wikipedia.org/wiki/Gauss%E2%80%93Newton_algorithm#)方法以及[Levenberg–Marquardt algorithm](https://en.wikipedia.org/wiki/Levenberg%E2%80%93Marquardt_algorithm)。这类数值求解方法从效率和准确性上对于SLAM大规模优化变量以及非线性优化问题都没有充分利用。Schur补就是这样的一种变种用来针对每次迭代过程中进行效率的提升。

## Schur-complememt方便最小二乘问题的求解
下面给出Schur-complement的主要公式：
$$
{\begin{aligned}
M&={\begin{bmatrix}A&B\\C&D\end{bmatrix}}={\begin{bmatrix}I_{p}&0\\CA^{-1}&I_{q}\end{bmatrix}}{\begin{bmatrix}A&0\\0&D-CA^{-1}B\end{bmatrix}}{\begin{bmatrix}I_{p}&A^{-1}B\\0&I_{q}\end{bmatrix}},\\
M^{-1}&={\begin{bmatrix}A^{-1}+A^{-1}B(M/A)^{-1}CA^{-1}&-A^{-1}B(M/A)^{-1}\\-(M/A)^{-1}CA^{-1}&(M/A)^{-1}\end{bmatrix}},\\
M/A&:=D-CA^{-1}B.
\end{aligned}}.
$$
其中$M$是任一矩阵，$A$是一个可逆的矩阵。

通过上述矩阵可以Gaussian消元的方式处理如下的线性化后的方程：
$$
\begin{bmatrix}A&B\\C&D\end{bmatrix}\begin{bmatrix}x\\y\end{bmatrix}=\begin{bmatrix}u\\v\end{bmatrix}
$$
假设A是可逆的，整理后可以得到：
$$
{\displaystyle x=A^{-1}(u-By)}\\
{\displaystyle \left(D-CA^{-1}B\right)y=v-CA^{-1}u},
$$
因此可以解决线性最小二乘问题。

## Gaussian-Newton(GN) 方法中出现线性最小二乘问题
GN方法是将原问题线性化后优化的主要方法（有很多理论性质，不在这里过多讨论），因此将BA问题线性化后可以得到如下形式的优化问题：
$$\text{argmin}_{x\in\mathcal{X}}\|r(x)\|^2 \implies \text{argmin}_{x\in\mathcal{X}}\|r(x_0)+H\delta x\|^2$$
因为$H$不是一个方阵，因此可以通过乘以$H^\top$的方式将这个问题写出来：
$$
-H^\top H\delta x=H^\top r(x_0),
$$
接下来我们就可以用shur补的方式代入求解。

## 常见的技巧：QR分解
$H$的矩阵维度为$2n*(6+3n)$,其中包括$H_p$和$H_f$,指的是位姿的雅克比和路标的雅克比。这一步经常采用QR分解减少计算。（[QR分解](https://en.wikipedia.org/wiki/QR_decomposition)主要是一种分解成orthonormal matrix Q和an upper triangular matrix R的方法,不在这里介绍。）

文章[Square Root Bundle Adjustment for Large-Scale Reconstruction](https://openaccess.thecvf.com/content/CVPR2021/papers/Demmel_Square_Root_Bundle_Adjustment_for_Large-Scale_Reconstruction_CVPR_2021_paper.pdf)中采用的方案是将$H_f$分解成两部分（公式15）。
其中
$$
H_f:=QR=\begin{bmatrix}
    Q_1&Q_2
\end{bmatrix}_{2n\times 2n}R_{2n\times 3n}，
$$
值得注意的是这部分$H_f\in\mathbb{R}^{m*n}$是一个$m<n$的矩阵。

原优化问题整理成如下的形式：
$$
{\begin{aligned}
 \|r(x_0)+H\delta x\|^2 &=\|r(x_0)+H_p\delta p+H_f\delta f\|^2\\
&=\|r(x_0)+H_p\delta p+H_f\delta f\|^2\\
&=\|Q^\top r(x_0)+Q^\top H_p\delta p+R\delta f\|^2
\end{aligned}}
$$

这里需要提一句，这个问题和原问题是等价问题。没有丢失任何信息。接下来阐述如何把这个思路放在滑窗里面。
