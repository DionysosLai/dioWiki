---
title: "The Rejection Method"
source: "https://pbr-book.org/4ed/Sampling_Algorithms/The_Rejection_Method"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## A.3 The Rejection Method
> ## A.3 拒绝方法

Many functions cannot be integrated in order to normalize them to find their PDFs.
> 许多函数无法通过积分来归一化以求出其 PDF。 Even given a PDF, it is often not possible to invert the associated CDF to generate samples using the inversion method. In such cases, the *rejection method* can be useful: it is a technique for generating samples according to a function’s distribution without needing to do either of these steps. Assume that we want to draw samples from some function where we have some PDF that satisfies for a constant, and suppose that we do know how to sample from. The rejection method is then:

This procedure repeatedly chooses a pair of random variables. If the point lies under, then the sample is accepted. Otherwise, it is rejected and a new sample pair is chosen. This idea is illustrated in Figure [A.2](#fig:rejection-sample); it works in any number of dimensions. It should be evident that the efficiency of this scheme depends on how tightly bounds.

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf02.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf02.svg)

Figure A.2: Rejection sampling generates samples according to the distribution of a function even if ’s PDF is unknown or its CDF cannot be inverted. If some distribution and a scalar constant are known such that, then samples can be drawn from and randomly accepted in a way that causes the accepted samples to be from ’s distribution. The closer the fit of to, the more efficient this process is.

For example, suppose we want to select a uniformly distributed point inside a unit disk. Using the rejection method, we simply select a random position inside the circumscribed square and return it if it falls inside the disk. This process is shown in Figure [A.3](#fig:rejection-sample-disk).

[![](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf03.svg)](https://pbr-book.org/4ed/Sampling_Algorithms/phaaaf03.svg)

Figure A.3: Rejection Sampling a Disk. One approach to finding uniform points in the unit disk is to sample uniform random points in the unit square and reject all that lie outside the disk (red points). The remaining points will be uniformly distributed within the disk.

The function [RejectionSampleDisk()](#RejectionSampleDisk) implements this algorithm. A similar approach will work to generate uniformly distributed samples on the inside of any complex shape as long as it has an inside–outside test.

<<Sampling Function Definitions>>+=

[Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) RejectionSampleDisk([RNG](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG) & [rng](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG::UniformFloat)) { [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p; do { p.x = 1 - 2 \* [rng](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG::UniformFloat).Uniform<Float>(); p.y = 1 - 2 \* [rng](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RNG::UniformFloat).Uniform<Float>(); } while ([Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (p.x) + [Sqr](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Sqr) (p.y) > 1); return p; }

In general, the efficiency of rejection sampling depends on the percentage of samples that are expected to be rejected. For [RejectionSampleDisk()](#RejectionSampleDisk), this is easy to compute. It is the area of the disk divided by the area of the square:. If the method is applied to generate samples in hyperspheres in the general -dimensional case, however, the volume of an -dimensional hypersphere goes to 0 as increases, and this approach becomes increasingly inefficient.

Rejection sampling is not used in any of the Monte Carlo algorithms currently implemented in pbrt. We will normally prefer to find distributions that are similar to the function that can be sampled directly, so that well-distributed sample points in can be mapped to sample points that are in turn well distributed. Nevertheless, rejection sampling is an important technique to be aware of, particularly when debugging Monte Carlo implementations. For example, if one suspects the presence of a bug in code that draws samples from some distribution using the inversion method, then one can replace it with a straightforward implementation based on the rejection method and see if the Monte Carlo estimator converges to the same value. Of course, it is necessary to take many samples in situations like these, so that variance in the estimates does not mask errors.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[采样与重建]]
- [[蒙特卡洛积分]]

**同章节**:
- [[A Sampling Algorithms]]
- [[A.1 The Alias Method]]
- [[A.2 Reservoir Sampling]]
- [[A.4 Sampling 1D Functions]]
- [[A.5 Sampling Multidimensional Functions]]
