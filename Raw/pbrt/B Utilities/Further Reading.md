---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Utilities/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
*Hacker’s Delight* (Warren [^27]) is a delightful and thought-provoking exploration of bit-twiddling algorithms like those used in some of the utility routines in this appendix. Sean Anderson ([^1]) has a Web page filled with a collection of bit-twiddling techniques like the ones in [IsPowerOf2()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#IsPowerOf2) and [RoundUpPow2()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#RoundUpPow2) at [graphics.stanford.edu/~seander/bithacks.html](http://graphics.stanford.edu/~seander/bithacks.html).

The MurmurHash hashing function that is wrapped by pbrt ’s [Hash()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Hash) and [HashBuffer()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#HashBuffer) functions is due to Appleby ([^2]) and the implementation of [MixBits()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#MixBits) is due to Stafford ([^26]), who found the various constant values used in the implementation via search.

The inverse bilinear interpolation function implemented in [InvertBilinear()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#InvertBilinear) is due to Quilez ([^23]) and [SinXOverX()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SinXOverX) is thanks to Hatch ([^11]).

The algorithm implemented in [TwoSum()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#TwoSum) is due to Møller ([^17]) and Knuth ([^15]), and the FMA-based [TwoProd()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#TwoProd) was developed by Ogita et al. ([^21]). The approach used in the [CompensatedSum](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#CompensatedSum) class is due to Kahan ([^14]). The approach used in [DifferenceOfProducts()](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#DifferenceOfProducts) is also attributed to Kahan; its error was analyzed by Jeannerod et al. ([^13]).

Welford ([^28]) developed the algorithm that is implemented in the [VarianceEstimator](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#VarianceEstimator) class. Its Merge() method is based on an algorithm developed by Chan et al. ([^7]).

Atkinson’s book ([^3]) on numerical analysis discusses algorithms for matrix inversion and solving linear systems. See Moore’s book ([^19]) for an introduction to interval arithmetic.

Farin’s book ([^9]) is a good introduction to splines. The blossoming approach was introduced by Ramshaw ([^25]); his report remains a readable introduction to the topic. A subsequent publication drew further connections to polar forms and related work ([Ramshaw 1989](#cite:Ramshaw1989)).

The PCG random number generator was developed by O’Neill ([^20]). The paper describing its implementation is well written and also features extensive discussion of a range of previous pseudo-random number generators and the challenges that they have faced in passing rigorous tests of their quality (L’Ecuyer and Simard [^16]).

The article “UTF-8 Everywhere” by Radzivilovsky et al. ([^24]) is a good introduction to Unicode and also makes a strong case for adopting the UTF-8 representation. pbrt follows the approach they propose for interoperating with Windows’s UTF-16-based APIs. At over 1,000 pages, the length of the official Unicode specification gives some sense of the complexities in representing multi-lingual text ([Unicode Consortium 2020](#cite:Unicode2020)).

Gamma correction has a long history in computer graphics. Poynton ([2002a](#cite:Poynton02color), [2002b](#cite:Poynton02)) has written comprehensive FAQs on issues related to color representation and gamma correction. The sRGB encoding was described by the International Electrotechnical Commission ([^12]). See Gritz and d’Eon ([^10]) for a detailed discussion of the implications of gamma correction for rendering and how to correctly account for it in rendering systems.

McKenney’s book on parallel programming is wonderfully written and has comprehensive coverage of the underlying issues, as well as many useful techniques for high-performance parallel programming on CPUs ([^18]). Drepper’s paper ([^8]) is a useful resource for understanding performance issues related to caches, cache coherence, and main memory access, particularly in multicore systems.

Boehm’s paper “Threads Cannot Be Implemented as a Library” ([^6]) makes the remarkable (and disconcerting) observation that multi-threading cannot be reliably implemented without the compiler having explicit knowledge of the fact that multi-threaded execution is expected. Boehm presented a number of examples that demonstrate the corresponding dangers in 2005-era compilers and language standards like C and C++ that did not have awareness of threading. Fortunately, the C++11 and C11 standards addressed the issues that he identified.

pbrt ’s parallel for loop–based approach to multi-threading is a widely used technique for multi-threaded programming; the OpenMP standard supports a similar construct (and much more) (OpenMP Architecture Review Board [^22]). A slightly more general model for multi-core parallelism is available from *task systems*, where computations are broken up into a set of independent tasks that can be executed concurrently. That model is supported through [RunAsync()](https://pbr-book.org/4ed/Utilities/Parallelism.html#RunAsync). Blumofe et al. ([^5]) described the task scheduler in Cilk, and Blumofe and Leiserson ([^4]) described the work-stealing algorithm that is the mainstay of many current high-performance task systems.

### References

[^1]: Anderson, S. 2004. Bit twiddling hacks. [graphics.stanford.edu/ seander/bithacks.html](http://graphics.stanford.edu/%C2%A0seander/bithacks.html)

[^2]: Appleby, A. 2011. MurmurHash3. [https://sites.google.com/site/murmurhash/](https://sites.google.com/site/murmurhash/)

[^3]: Atkinson, K. 1993. *Elementary Numerical Analysis.* New York: John Wiley & Sons.

[^4]: Blumofe, R., and C. Leiserson. 1999. Scheduling multithreaded computations by work stealing. *Journal of the ACM* *46*  (5), 720–48.

[^5]: Blumofe, R., C. Joerg, B. Kuszmaul, C. Leiserson, K. Randall, and Y. Zhou. 1996. Cilk: An efficient multithreaded runtime system. *Journal of Parallel and Distributed Computing* *37*  (1), 55–69.

[^6]: Boehm, H.-J. 2005. Threads cannot be implemented as a library. *ACM SIGPLAN Notices* *40*  (6), 261–68.

[^7]: Chan, T. F., G. Golub, R. J. LeVeque. 1979. Updating formulae and a pairwise algorithm for computing sample variances. *Technical Report STAN-CS-79-773*, Department of Computer Science, Stanford University.

[^8]: Drepper, U. 2007. What every programmer should know about memory. [people.redhat.com/drepper/cpumemory.pdf](http://people.redhat.com/drepper/cpumemory.pdf)

[^9]: Farin, G. 2001. *Curves and Surfaces for CAGD: A Practical Guide* (5th ed.). San Francisco: Morgan Kaufmann.

[^10]: Gritz, L., and E. d’Eon. 2008. The importance of being linear. In H. Nguyen (ed.), *GPU Gems 3*, 529–42. Boston, Massachusetts: Addison-Wesley.

[^11]: Hatch, D. 2003. The right way to calculate stuff. [http://www.plunk.org/ hatch/rightway.html](http://www.plunk.org/%C2%A0hatch/rightway.html)

[^12]: International Electrotechnical Commission (IEC). 1999. Multimedia systems and equipment—Colour measurement and management—Part 2-1: Colour management—Default RGB colour space—sRGB. IEC Standard 61966-2-1.

[^13]: Jeannerod, C.-P., N. Louvet, and J.-M. Muller. 2013. Further analysis of Kahan’s algorithm for the accurate computation of determinants. *Mathematics of Computation* *82*  (284), 2245–64.

[^14]: Kahan, W. 1965. Further remarks on reducing truncation errors. *Communications of the ACM* *8*  (1), 40.

[^15]: Knuth, D. E. 1969. *The Art of Computer Programming: Seminumerical Algorithms*. Reading, Massachusetts: Addison-Wesley.

[^16]: L’Ecuyer, P., and R. Simard. 2007. TestU01: A C library for empirical testing of random number generators. *ACM Transactions on Mathematical Software* *33*  (4), 22:1–40.

[^17]: Møller, O. 1965. Quasi double precision in floating-point arithmetic. *BIT Numerical Mathematics* *5*, 37–50.

[^18]: McKenney, P. E. 2021. *Is Parallel Programming Hard, and, If So, What Can You Do About It?* [https://mirrors.edge.kernel.org/pub/linux/kernel/people/paulmck/perfbook/perfbook.html](https://mirrors.edge.kernel.org/pub/linux/kernel/people/paulmck/perfbook/perfbook.html)

[^19]: Moore, R. E. 1966. *Interval Analysis*. Englewood Cliffs, New Jersey: Prentice Hall.

[^20]: O’Neill, M. 2014. PCG: A family of simple fast space-efficient statistically good algorithms for random number generation. Unpublished manuscript. [http://www.pcg-random.org/paper.html](http://www.pcg-random.org/paper.html)

[^21]: Ogita, T., S. M. Rump, and S. Oishi. 2005. Accurate sum and dot product. *SIAM Journal on Scientific Computing* *26*  (6), 1955–88.

[^22]: OpenMP Architecture Review Board. 2013. OpenMP Application Program Interface. [http://www.openmp.org/mp-documents/OpenMP4.0.0.pdf](http://www.openmp.org/mp-documents/OpenMP4.0.0.pdf)

[^23]: Quilez, I. 2010. Inverse bilinear interpolation. [https://www.iquilezles.org/www/articles/ibilinear/ibilinear.htm](https://www.iquilezles.org/www/articles/ibilinear/ibilinear.htm)

[^24]: Radzivilovsky, P., Y. Galka, and S. Novgorodov. 2012. UTF-8 everywhere. [http://utf8everywhere.org](http://utf8everywhere.org/)

[^25]: Ramshaw, L. 1987. Blossoming: A connect-the-dots approach to splines. *Digital Systems Research Center Technical Report*

[^26]: Stafford, D. 2011. Better bit mixing—improving on MurmurHash3’s 64-bit finalizer. [http://zimbry.blogspot.com/2011/09/better-bit-mixing-improving-on.html](https://zimbry.blogspot.com/2011/09/better-bit-mixing-improving-on.html)

[^27]: Warren, H. 2006. *Hacker’s Delight*. Reading, Massachusetts: Addison-Wesley.

[^28]: Welford, B. P. 1962. Note on a method for calculating corrected sums of squares and products. *Technometrics* *4*  (3), 419–20.