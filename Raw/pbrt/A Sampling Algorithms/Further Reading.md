---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Sampling_Algorithms/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
Rejection sampling was developed by von Neumann ([^19]) shortly after the Monte Carlo method was invented.

The alias method was introduced by Walker ([^21], [^22]). The algorithm that we have implemented to generate alias tables in the [AliasTable](https://pbr-book.org/4ed/Sampling_Algorithms/The_Alias_Method.html#AliasTable) class is due to Vose ([^20]). See Schwarz’s article ([^13]) for extensive information about implementing alias tables and related techniques.

A number of algorithms for reservoir sampling were described by Vitter ([^18]), though he credits the basic algorithm we outlined at the start of Section [A.2](https://pbr-book.org/4ed/Sampling_Algorithms/Reservoir_Sampling.html#sec:reservoir-sampling) to Alan Waterman. The weighted reservoir sampling algorithm in pbrt ’s [WeightedReservoirSampler](https://pbr-book.org/4ed/Sampling_Algorithms/Reservoir_Sampling.html#WeightedReservoirSampler) class is due to Chao ([^3]).

The square to disk mapping in Section [A.5.1](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_Multidimensional_Functions.html#sec:unit-disk-sample) was described by Shirley and Chiu ([^15]). The implementation here benefits by observations in Shirley’s 2011 blog by Dave Cline and the commenter “franz” that the logic could be simplified considerably from the original algorithm (Shirley [^14]). Articles by Shirley and collaborators describe a number of useful recipes for warping uniform random numbers to useful distributions for rendering ([Shirley 1992](#cite:Shirley92); [Shirley et al. 2019](#cite:Shirley2019)).

The summed-area table data structure was introduced by Crow ([^7]). Its use for efficiently sampling arbitrary rectangluar regions of images was demonstrated by Bitterli et al. ([^2]).

A number of additional sampling techniques have been developed for tabularized multidimensional distributions. Steigleder and McCool ([^16]) linearized 2D and higher dimensional domains into 1D using a Hilbert curve and then sampled using 1D samples over the 1D domain, which still maintains desirable stratification properties of the sampling distribution thanks to the spatial coherence preserving properties of the Hilbert curve. McCool and Harwood ([^10]) as well as Clarberg et al. ([^5]) described an approach for sampling images based on quadtrees that repeatedly transforms uniform sample values until they match a target distribution.

Lawrence et al. ([^8]) described an adaptive representation for tabularized CDFs, where the CDF is approximated with a piecewise-linear function with fewer, irregularly spaced vertices than the given CDF. This approach can substantially reduce storage requirements and improve lookup efficiency, taking advantage of the fact that large ranges of the CDF may be efficiently approximated with a single linear function.

The time spent searching the CDF when sampling from a tabularized distribution can be reduced with auxiliary data structures. Chen and Asau ([^4]) suggested the *guide table method*, where an additional array of offsets into the table gives a starting point for the search. Cline et al. ([^6]) introduced this approach to graphics and also presented a method based on approximating the inverse CDF as a piecewise-linear function of , thus enabling constant-time lookups at a cost of some accuracy. Binder and Keller ([^1]) presented algorithms for building and sampling from tabularized distributions that run efficiently on GPUs. Another innovative approach to sampling from such CDFs was described by Morrical and Zellmann ([^11]), who showed how hardware ray tracing capabilities could be used for this task. Vitsas et al. ([^17]) fit Gaussian mixture models to the sampling distribution of clear sky environment maps and showed a significant reduction in memory use with a sampling algorithm that does not require table search.

Arithmetic coding offers another interesting way to approach sampling from distributions (MacKay [^9], p. 118; Piponi [^12]). If we have a discrete set of probabilities from which we would like to generate samples, one way to approach the problem is to encode the CDF as a binary tree where each node splits the interval at some point and where, given a random sample , we determine which sample value it corresponds to by traversing the tree until we reach the leaf node for its sample value. Ideally, we would like leaf nodes that represent higher probabilities to be higher up in the tree, so that it takes fewer traversal steps to find them (and thus those more frequently generated samples can be found more quickly). Looking at the problem from this perspective, it can be shown that the optimal structure of such a tree is given by Huffman coding, which is normally used for compression.

### References

[^1]: Binder, N., and A. Keller. 2020. Massively parallel construction of radix tree forests for the efficient sampling of discrete or piecewise constant probability distributions. *Monte Carlo and Quasi-Monte Carlo Methods (MCQMC 2018)*. arXiv: 1902.05942 \[cs\].

[^2]: Bitterli, B., J. Novák, and W. Jarosz. 2015. Portal-masked environment map sampling. *Computer Graphics Forum (Proceedings of the 2015 Eurographics Symposium on Rendering)* *34*  (4), 13–19.

[^3]: Chao, M. T. 1982. A general purpose unequal probability sampling plan. *Biometrika* *69*  (3), 653–56.

[^4]: Chen, H. C., and Y. Asau. 1974. On generating random variates from an empirical distribution. *AIIE Transactions* *6*  (2), 163–66.

[^5]: Clarberg, P., W. Jarosz, T. Akenine-Möller, and H. W. Jensen. 2005. Wavelet importance sampling: Efficiently evaluating products of complex functions. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2005)* *24*  (3), 1166–75.

[^6]: Cline, D., A. Razdan, and P. Wonka. 2009. A comparison of tabular PDF inversion methods. *Computer Graphics Forum* *28*  (1), 154–60.

[^7]: Crow, F. C. 1984. Summed-area tables for texture mapping. *Computer Graphics (Proceedings of SIGGRAPH ’84)* *18*, 207–12.

[^8]: Lawrence, J., S. Rusinkiewicz, and R. Ramamoorthi. 2005. Adaptive numerical cumulative distribution functions for efficient importance sampling. *Rendering Techniques 2005: 16th Eurographics Workshop on Rendering*, 11–20.

[^9]: MacKay, D. 2003. *Information Theory, Inference, and Learning Algorithms*. Cambridge: Cambridge University Press.

[^10]: McCool, M. D., and P. K. Harwood. 1997. Probability trees. *Proceedings of Graphics Interface ’97*, 37–46.

[^11]: Morrical, N., and S. Zellmann. 2021. Inverse transform sampling using ray tracing hardware. In Marrs, A., P. Shirley, and I. Wald (eds.), *Ray Tracing Gems II*, 625–41. Berkeley: Apress.

[^12]: Piponi, D. 2012. Lossless decompression and the generation of random samples. [http://blog.sigfpe.com/2012/01/lossless-decompression-and-generation.html](http://blog.sigfpe.com/2012/01/lossless-decompression-and-generation.html)

[^13]: Schwarz, K. 2011. Darts, dice, and coins: Sampling from a discrete distribution. [http://www.keithschwarz.com/darts-dice-coins/](http://www.keithschwarz.com/darts-dice-coins/)

[^14]: Shirley, P. 2011. Improved code for concentric map. [http://psgraphics.blogspot.com/2011/01/improved-code-for-concentric-map.html](https://psgraphics.blogspot.com/2011/01/improved-code-for-concentric-map.html)

[^15]: Shirley, P., and K. Chiu. 1997. A low distortion map between disk and square. *Journal of Graphics Tools* *2*  (3), 45–52.

[^16]: Steigleder, M., and M. McCool. 2003. Generalized stratified sampling using the Hilbert curve. *Journal of Graphics Tools* *8*  (3), 41–47.

[^17]: Vitsas, N., K. Vardis, and G. Papaioannou. 2021. Sampling clear sky models using truncated Gaussian mixtures. *Proceedings of the Eurographics Symposium on Rendering*, 35–44.

[^18]: Vitter, J. S. 1985. Random sampling with a reservoir. *ACM Transactions on Mathematical Software*, *11*  (1), 37–57.

[^19]: von Neumann, J. 1951. Various techniques used in connection with random digits. *Journal of Research of the National Bureau of Standards, Applied Mathematics Series* *12*, 36–38.

[^20]: Vose, M. D. 1991. A linear algorithm for generating random numbers with a given distribution. *IEEE Transactions on Software Engineering* *17*  (9), 972–75.

[^21]: Walker, A. J. 1974. New fast method for generating discrete random numbers with arbitrary frequency distributions. *Electronics Letters* *10*  (8): 127–28.

[^22]: Walker, A. J. 1977. An efficient method for generating discrete random variables with general distributions. *ACM Transactions on Mathematical Software* *3*  (3), 253–56.

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
- [[A.3 The Rejection Method]]
- [[A.4 Sampling 1D Functions]]
- [[A.5 Sampling Multidimensional Functions]]
