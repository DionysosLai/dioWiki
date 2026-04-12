---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Monte_Carlo_Integration/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
The Monte Carlo method was introduced soon after the development of the digital computer by Stanislaw Ulam and John von Neumann ([Ulam et al. 1947](#cite:Ulam1947)), though it also seems to have been independently invented by Enrico Fermi ([Metropolis 1987](#cite:Metropolis1987)). An early paper on Monte Carlo was written by Metropolis and Ulam ([^14]).

Many books have been written on Monte Carlo integration. Hammersley and Handscomb ([^5]), Spanier and Gelbard ([^24]), and Kalos and Whitlock ([^9]) are classic references. More recent books on the topic include those by Sobol ([^23]), Fishman ([^2]), and Liu ([^12]). We have also found Owen’s in-progress book ([^17]) to be an invaluable resource. Motwani and Raghavan ([^15]) have written an excellent introduction to the broader topic of randomized algorithms.

Most of the functions of interest in rendering are nonnegative; applying importance sampling to negative functions requires special care. A straightforward option is to define a sampling distribution that is proportional to the absolute value of the function. See also Owen and Zhou ([^18]) for a more effective sampling approach for such functions.

Multiple importance sampling was developed by Veach and Guibas ([Veach and Guibas 1995](#cite:Veach95); [Veach 1997](#cite:VeachThesis)). Normally, a predetermined number of samples are taken using each sampling technique; see Pajot et al. ([^19]) and Lu et al. ([^13]) for approaches to adaptively distributing the samples over strategies in an effort to reduce variance by choosing those that are the best match to the integrand. Grittmann et al. ([^4]) tracked the variance of each sampling technique and then dynamically adjusted the MIS weights accordingly. The MIS compensation approach was developed by Karlík et al. ([^10]).

Sbert and collaborators ([^21], [^20], [^22]) have performed further variance analysis on MIS estimators and have developed improved methods based on allocating samples according to the variance and cost of each technique. Kondapaneni et al. ([^11]) considered the generalization of MIS to include negative weights and derived optimal estimators in that setting. West et al. ([^26]) considered the case where a continuum of sampling techniques are available and derived an optimal MIS estimator for that case, and Grittmann et al. ([^3]) have developed improved MIS estimators when correlation is present among samples (as is the case, for example, with bidirectional light transport algorithms).

Heitz ([^7]) described an inversion-based sampling method that can be applied when CDF inversion of a 1D function is not possible. It is based on sampling from a second function that approximates the first and then using a second random variable to adjust the sample to match the original function’s distribution. An interesting alternative to manually deriving sampling techniques was described by Anderson et al. ([^1]), who developed a domain-specific language for sampling where probabilities are automatically computed, given the implementation of a sampling algorithm. They showed the effectiveness of their approach with succinct implementations of a number of tricky sampling techniques.

The numerically stable sampling technique used in [SampleLinear()](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#SampleLinear) is an application of Muller’s method ([^16]) due to Heitz ([^7]).

In applications of Monte Carlo in graphics, the integrand is often a product of factors, where no sampling distribution is available that fits the full product. While multiple importance sampling can give reasonable results in this case, at least minimizing variance from ineffective sampling techniques, sampling the full product is still preferable. Talbot et al. ([^25]) applied *importance resampling* to this problem, taking multiple samples from some distribution and then choosing among them with probability proportional to the full integrand. More recently, Hart et al. ([^6]) presented a simple technique based on warping uniform samples that can be used to approximate product sampling. For more information on this topic, see also the “Further Reading” sections of Chapters [13](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection.html#chap:light-transport) and [14](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering.html#chap:volume-integration), which discuss product sampling approaches in the context of specific light transport algorithms.

Debugging Monte Carlo algorithms can be challenging, since it is their behavior in expectation that determines their correctness: it may be difficult to tell if the program execution for a particular sample is correct. Statistical tests can be an effective approach for checking their correctness. See the papers by Subr and Arvo ([2007a](#cite:Subr2007a)) and by Jung et al. ([^8]) for applicable techniques.

See also the “Further Reading” section in Appendix [A](https://pbr-book.org/4ed/Sampling_Algorithms.html#chap:sampling-algorithms), which has information about the sampling algorithms implemented there as well as related approaches.

### References

[^1]: Anderson, L., T.-M. Li, J. Lehtinen, and F. Durand. 2017. Aether: An embedded domain specific sampling language for Monte Carlo rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2017)* *36*  (4), 99:1–16.

[^2]: Fishman, G. S. 1996. *Monte Carlo: Concepts, Algorithms, and Applications*. New York: Springer-Verlag.

[^3]: Grittmann, P., I. Georgiev, and P. Slusallek. 2021. Correlation-aware multiple importance sampling for bidirectional rendering algorithms. *Computer Graphics Forum (Proceedings of Eurographics)* *40*  (2), 231–38.

[^4]: Grittmann, P., I. Georgiev, P. Slusallek, and J. Křivánek. 2019. Variance-aware multiple importance sampling. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2019)* *38*  (6), 152:1–9.

[^5]: Hammersley, J., and D. Handscomb. 1964. *Monte Carlo Methods*. New York: John Wiley.

[^6]: Hart, D., M. Pharr, T. Müller, W. Lopes, M. McGuire, and P. Shirley. 2020. Practical product sampling by fitting and composing warps. *Computer Graphics Forum* *39*  (4), 149–58.

[^7]: Heitz, E. 2020. Can’t invert the CDF? The triangle-cut parameterization of the region under the curve. *Computer Graphics Forum* *39*  (4), 121–32.

[^8]: Jung, A., J. Hanika, and C. Dachsbacher. 2020. Detecting bias in Monte Carlo renderers using Welch’s t-test. *Journal of Computer Graphics Techniques (JCGT)* *9*  (2), 1–25.

[^9]: Kalos, M. H., and P. A. Whitlock. 1986. *Monte Carlo Methods: Volume I: Basics*. New York: Wiley.

[^10]: Karlík, O., M. Šik, P. Vévoda, T. Skřivan, and J. Křivánek. 2019. MIS compensation: Optimizing sampling techniques in multiple importance sampling. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *38*  (6), 151:1–12.

[^11]: Kondapaneni, I., P. Vévoda, P. Grittmann, T. Skřivan, P. Slusallek, and J. Křivánek. 2019. Optimal multiple importance sampling. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *37*  (4), 37:1–14.

[^12]: Liu, J. S. 2001. *Monte Carlo Strategies in Scientific Computing*. New York: Springer-Verlag.

[^13]: Lu, H., R. Pacanowski, and X. Granier. 2013. Second-order approximation for variance reduction in multiple importance sampling. *Computer Graphics Forum* *32*  (7), 131–36.

[^14]: Metropolis, N., and S. Ulam. 1949. The Monte Carlo method. *Journal of the American Statistical Association* *44*  (247), 335–41.

[^15]: Motwani, R., and P. Raghavan. 1995. *Randomized Algorithms*. Cambridge, U.K.: Cambridge University Press.

[^16]: Muller, D. E. 1956. A method for solving algebraic equations using an automatic computer. *Mathematical Tables and Other Aids to Computation* *10*  (56), 208–15.

[^17]: Owen, A. B. 2019. Monte Carlo theory, methods and examples. [https://statweb.stanford.edu/ owen/mc/](https://statweb.stanford.edu/%C2%A0owen/mc/)

[^18]: Owen, A., and Y. Zhou. 2000. Safe and effective importance sampling. *Journal of the American Statistical Association* *95*  (449), 135–43.

[^19]: Pajot, A., L. Barthe, M. Paulin, and P. Poulin. 2011. Representativity for robust and adaptive multiple importance sampling. *IEEE Transactions on Visualization and Computer Graphics* *17*  (8), 1108–21.

[^20]: Sbert, M., and V. Havran. 2017. Adaptive multiple importance sampling for general functions. *The Visual Computer* *33*, 845–55.

[^21]: Sbert, M., V. Havran, and L. Szirmay-Kalos. 2016. Variance analysis of multi-sample and one-sample multiple importance sampling. *Computer Graphics Forum* *35*  (7), 451–60.

[^22]: Sbert, M., V. Havran, and L. Szirmay-Kalos. 2018. Multiple importance sampling revisited: Breaking the bounds. *EURASIP Journal on Advances in Signal Processing* *15*, 1–15.

[^23]: Sobol, I. M. 1994. *A Primer for the Monte Carlo Method*. Boca Raton: CRC Press.

[^24]: Spanier, J., and E. M. Gelbard. 1969. *Monte Carlo Principles and Neutron Transport Problems*. Reading, Massachusetts: Addison-Wesley.

[^25]: Talbot, J., D. Cline, and P. Egbert. 2005. Importance resampling for global illumination. *Rendering Techniques 2005: 16th Eurographics Workshop on Rendering*, 139–46.

[^26]: West, W., I. Georgiev, A. Gruson, and T. Hachisuka. 2020. Continuous multiple importance sampling. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *39*  (4), 136:1–12.