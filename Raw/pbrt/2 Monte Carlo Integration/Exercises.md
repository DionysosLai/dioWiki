---
title: "Exercises"
source: "https://pbr-book.org/4ed/Monte_Carlo_Integration/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
1. ② Write a program that compares Monte Carlo and one or more alternative numerical integration techniques. Structure this program so that it is easy to replace the particular function being integrated. Verify that the different techniques compute the same result (given a sufficient number of samples for each of them). Modify your program so that it draws samples from distributions other than the uniform distribution for the Monte Carlo estimate, and verify that it still computes the correct result when the correct estimator, Equation (2.7), is used.

> 1. ② 编写一个程序，比较蒙特卡洛方法和一种或多种替代数值积分技术。设计该程序使其易于替换被积分的特定函数。验证不同技术计算出相同的结果（给定每种技术足够数量的样本）。修改程序使其从均匀分布以外的分布中抽取样本进行蒙特卡洛估计，并验证使用正确的估计量（方程 2.7）时仍然计算出正确的结果。

2. ① Write a program that computes unbiased Monte Carlo estimates of the integral of a given function. Compute an estimate of the variance of the estimates by performing a series of trials with successively more samples and computing the mean squared error for each one. Demonstrate numerically that variance decreases at a rate of O(1/n).

> 2. ① 编写一个程序，计算给定函数积分的无偏蒙特卡洛估计。通过执行一系列样本数递增的试验并计算每次的均方误差来估计估计值的方差。用数值方法证明方差以 O(1/n) 的速率下降。

3. ② The algorithm for sampling the linear interpolation function in Section 2.3.2 implicitly assumes that a ≥ 0 and b ≥ 0. Generalize SampleLinear() and the associated PDF and inversion functions to handle the case where the function is always negative as well as the case where it crosses zero due to a and b having different signs.

> 3. ② 第 2.3.2 节中线性插值函数的采样算法隐含假设 a ≥ 0 且 b ≥ 0。推广 SampleLinear() 及其关联的 PDF 和反转函数，以处理函数始终为负的情况以及由于 a 和 b 符号不同导致函数过零的情况。

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[蒙特卡洛积分]]

**同章节**:
- [[2 Monte Carlo Integration]]
- [[2.1 Monte Carlo Basics]]
- [[2.2 Improving Efficiency]]
- [[2.3 Sampling Using the Inversion Method]]
- [[2.4 Transforming between Distributions]]
