---
title: "Monte Carlo Integration"
source: "https://pbr-book.org/4ed/Monte_Carlo_Integration"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 2 Monte Carlo Integration

Rendering is full of integration problems. In addition to the light transport equation (1.1), in the following chapters we will see that integral equations also describe a variety of additional quantities related to light, including the sensor response in a camera, the attenuation and scattering of light in participating media, and scattering from materials like skin. These integral equations generally do not have analytic solutions, so we must turn to numerical methods. Although standard numerical integration techniques like trapezoidal integration or Gaussian quadrature are effective at solving low-dimensional smooth integrals, their rate of convergence is poor for the higher dimensional and discontinuous integrals that are common in rendering. Monte Carlo integration techniques provide one solution to this problem. They use random sampling to evaluate integrals with a convergence rate that is independent of the dimensionality of the integrand.

> 渲染充满了积分问题。除了光传输方程（1.1）之外，在接下来的章节中我们将看到，积分方程还描述了与光相关的各种其他量，包括相机的传感器响应、参与介质中光的衰减和散射，以及来自皮肤等材质的散射。这些积分方程通常没有解析解，因此我们必须转向数值方法。虽然标准数值积分技术（如梯形积分或高斯求积）对低维光滑积分很有效，但对渲染中常见的高维和不连续积分收敛速度很差。蒙特卡洛积分技术提供了一种解决方案。它们使用随机采样来计算积分，收敛速率与被积函数的维度无关。

Monte Carlo integration has the useful property that it only requires the ability to evaluate an integrand at arbitrary points in the domain in order to estimate the value of its integral. This property not only makes Monte Carlo easy to implement but also makes the technique applicable to a broad variety of integrands. It has a natural extension to multidimensional functions; in Chapter 13, we will see that the light transport algorithm implemented in the RandomWalkIntegrator can be shown to be estimating the value of an infinite-dimensional integral.

> 蒙特卡洛积分有一个有用的性质：它只需要能够在域中的任意点计算被积函数的值就可以估计其积分值。这一性质不仅使蒙特卡洛易于实现，还使该技术适用于广泛的被积函数。它自然地扩展到多维函数；在第 13 章中，我们将看到 RandomWalkIntegrator 中实现的光传输算法可以被证明是在估计一个无穷维积分的值。

Judicious use of randomness has revolutionized the field of algorithm design. Randomized algorithms fall broadly into two classes: *Las Vegas* and *Monte Carlo*. Las Vegas algorithms are those that use randomness but always give the same result in the end (e.g., choosing a random array entry as the pivot element in Quicksort). Monte Carlo algorithms, on the other hand, give different results depending on the particular random numbers used along the way but give the right answer *on average*. So, by averaging the results of several runs of a Monte Carlo algorithm (on the same input), it is possible to find a result that is statistically very likely to be close to the true answer.

> 对随机性的审慎使用已经革新了算法设计领域。随机算法大致分为两类：*拉斯维加斯*算法和*蒙特卡洛*算法。拉斯维加斯算法使用随机性但最终总是给出相同的结果（例如，在快速排序中选择随机数组元素作为枢轴）。而蒙特卡洛算法则根据过程中使用的特定随机数给出不同的结果，但*平均来看*给出正确答案。因此，通过对蒙特卡洛算法的多次运行结果取平均（对相同输入），可以找到统计上很可能接近真实答案的结果。

The following sections discuss the basic principles of Monte Carlo integration, focusing on those that are widely used in pbrt. See also Appendix A, which has the implementations of additional Monte Carlo sampling functions that are more rarely used in the system.

> 以下各节讨论蒙特卡洛积分的基本原理，重点关注那些在 pbrt 中广泛使用的内容。另请参见附录 A，其中有系统中较少使用的额外蒙特卡洛采样函数的实现。