---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Sampling_and_Reconstruction/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
Heckbert ([1990a](#cite:Heckbert90pixel)) wrote an article that explains possible pitfalls when using floating-point coordinates for pixels and develops the conventions that are introduced in Section [8.1.4](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Theory.html#sec:pixel-concepts).

#### Sampling Theory and Aliasing

One of the best books on signal processing, sampling, reconstruction, and the Fourier transform is Bracewell’s *The Fourier Transform and Its Applications* ([^13]). Glassner’s *Principles of Digital Image Synthesis* ([^32]) has a series of chapters on the theory and application of uniform and nonuniform sampling and reconstruction to computer graphics. For an extensive survey of the history of and techniques for interpolation of sampled data, including the sampling theorem, see Meijering ([^58]). Unser ([^86]) also surveyed developments in sampling and reconstruction theory, including the move away from focusing purely on band-limited functions. For more recent work in this area, see Eldar and Michaeli ([^27]).

Crow ([^19]) was the first to identify aliasing as a major source of artifacts in computer-generated images. Using nonuniform sampling to turn aliasing into noise was introduced by Cook ([^18]) and Dippé and Wold ([^21]); their work was based on experiments by Yellot ([^91]), who investigated the distribution of photoreceptors in the eyes of monkeys. Dippé and Wold also first introduced the pixel filtering equation to graphics and developed a Poisson sample pattern with a minimum distance between samples.

Mitchell ([^60], [^61]) extensively investigated sampling patterns for ray tracing. His papers on this topic have many key insights, especially on the importance of blue noise distributions for sampling patterns. See also Ulichney ([^84]), who demonstrated the effectiveness of blue noise in the context of dithering.

Compressed sensing is an alternative approach to sampling where the required sampling rate depends on the sparsity of the signal, not its frequency content. Sen and Darabi ([^76]) applied compressed sensing to rendering, allowing them to generate high-quality images at very low sampling rates.

Lessig et al. ([^54]) proposed a general framework for constructing quadrature rules tailored to specific integration problems such as stochastic ray tracing, spherical harmonics projection, and scattering by surfaces. When targeting band-limited functions, their approach subsumes the frequency space approach presented in this chapter. An excellent tutorial about the underlying theory of *reproducing kernel bases* is provided in the article’s supplemental material.

#### Analysis of Monte Carlo Integration

Starting with Ramamoorthi and Hanrahan’s ([^74]) and Durand et al.’s foundational work ([^25]), a number of researchers have analyzed light transport and Monte Carlo integration using Fourier analysis. Singh et al.’s survey ([2019a](#cite:Singh2019:star)) has comprehensive coverage of work in this area.

Durand ([^24]) was the first to express variance using Fourier analysis, converting the Monte Carlo estimator to the continuous form (our Equation ([8.10](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_and_Integration.html#eq:mc-estimator-continuous))) in order to demonstrate that the sampling rate only has to equal the function’s highest frequency in order to achieve zero variance. He further derived the variance in terms of the integral of the product of the function’s and sampling pattern’s power spectra.

Subr and Kautz ([^82]) subsequently expressed variance in terms of a product of the variance of the sampling pattern and the function being integrated in frequency space. Pilleboue et al. ([^72]) applied homogenization to sampling patterns in order to express variance in terms of the power spectra in a more general setting than Durand ([^24]) and extended the analysis to functions on the sphere. They further derived asymptotic convergence rates for various sampling techniques and showed that they matched empirical measurements. These results not only provided a theoretical basis to explain earlier measurements made by Mitchell ([^63]) but included the surprising result that Poisson disk patterns have asymptotically worse convergence rates than simple jittered patterns.

Öztireli ([^1]) applied point process statistics to study stochastic sampling patterns for integration, deriving closed-form expressions for bias and variance of a number of approaches and analyzing integrands with discontinuities due to visibility. Singh and Jarosz ([^79]) analyzed the variance of anisotropic sampling patterns (of which jittered sampling is a notable example), and Singh et al. ([^80]) investigated the variance of sampling with line segments rather than points. The use of Fourier series to analyze sampling patterns was introduced by Singh et al. ([2019b](#cite:Singh2019:fourier)), which allowed the analysis of nonhomogeneous sampling patterns and also made it possible to incorporate the effect of importance sampling.

Öztireli ([^2]) provided a comprehensive review of work in blue noise sampling for rendering through 2019 and then applied the theory of stochastic point processes to derive the expected error spectrum from sampling a function with a given sampling technique in terms of the associated power spectra. This result makes clear why having minimal power in the low frequencies and as close to uniform unit power as possible at higher frequencies is best for antialiasing.

#### Sample Generation Algorithms

After the introduction of jittered sampling, Mitchell ([^60]) introduced an approach to generate sampling patterns with good blue noise characteristics using error diffusion and later developed an algorithm for generating sampling patterns that were also optimized for sampling motion blur and depth of field ([^61]). A key observation in the second paper was that a -dimensional Poisson disk distribution is not the ideal one for general integration problems in graphics; while it is useful for the projection of the first two dimensions on the image plane to have the Poisson-disk property, it is important that the other dimensions be more widely distributed than the Poisson-disk quality alone guarantees.

The utility of such approaches was recently understood more widely after work by Georgiev and Fajardo ([^31]), who also described a method to generate tables of samples where nearby points are decorrelated for such applications. Heitz and Belcour ([^37]) developed a technique that permutes random seeds across nearby pixels in order to decorrelate the *error* in the image, rather than just the sample values themselves.

The blue noise points provided via the [BlueNoise()](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Theory.html#BlueNoise) function are thanks to Peters ([^70]) and were generated using Ulichney’s “void and cluster” algorithm ([^85]).

Chiu, Shirley, and Wang ([^16]) suggested a *multi-jittered* 2D sampling technique based on randomly shuffling the and coordinates of a canonical jittered pattern that combines the properties of stratified and Latin hypercube sampling patterns. More recently, Kensler ([^48]) showed that using the same permutation for both dimensions with their method gives much better results than independent permutations; he showed that this approach gives lower discrepancy than the Sobol’ pattern while also maintaining the perceptual advantages of turning aliasing into noise due to using jittered samples. Christensen et al. further improved this approach ([^17]), generating point sets that were also stratified with respect to the elementary intervals and had good blue noise properties. Pharr ([^71]) proposed a more efficient algorithm to generate these points, though Grünschloß et al. ([^34]) had earlier developed an efficient elementary interval test that is similar to the one described there.

Lagae and Dutré ([2008c](#cite:Lagae08c)) surveyed the state of the art in generating Poisson disk sample patterns and compared the quality of the point sets that various algorithms generated. Reinert et al. ([^75]) proposed a construction for -dimensional Poisson disk samples that retain their characteristic sample separation under projection onto lower-dimensional subsets, which ensures good performance if the variation in the function is focused along only some of the dimensions.

Jarosz et al. ([^40]) applied *orthogonal array sampling* to generating multidimensional sample points that retain good distribution across lower-dimensional projections and showed that this approach gives much better results than randomly padding lower-dimensional samples as pbrt does in the [PaddedSobolSampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#PaddedSobolSampler), for example.

The error analysis framework derived in Öztireli’s paper ([^2]) further makes it possible to express the desired properties of a point set in a form that is suitable to solve as an optimization problem. This made it possible to generate point sets with superior antialiasing capabilities to previous approaches. (That paper also includes an extensive review of the state of the art in blue noise and Poisson disk sample point generation.)

#### Low-Discrepancy Sampling and QMC

Shirley ([^78]) first introduced the use of discrepancy to evaluate the quality of sample patterns in computer graphics. This work was built upon by Mitchell ([^62]), Dobkin and Mitchell ([^22]), and Dobkin, Eppstein, and Mitchell ([^23]). One important observation in Dobkin et al.’s paper is that the box discrepancy measure used in this chapter and in other work that applies discrepancy to pixel sampling patterns is not particularly appropriate for measuring a sampling pattern’s accuracy at randomly oriented edges through a pixel and that a discrepancy measure based on random edges should be used instead.

Mitchell’s first paper on discrepancy introduced the idea of using deterministic low-discrepancy sequences for sampling, removing all randomness in the interest of lower discrepancy ([^62]). The seminal book on quasi-random sampling and algorithms for generating low-discrepancy patterns was written by Niederreiter ([^65]). For a more recent treatment, see Dick and Pillichshammer’s excellent book ([^20]).

Keller and collaborators have investigated quasi-random sampling patterns for a variety of applications in graphics ([Keller 1996](#cite:Keller96), [^44], [^45], [Kollig and Keller 2000](#cite:Kollig00)). Keller’s “Quasi-Monte Carlo image synthesis in a nutshell” ([^47]) is a good introduction to quasi–Monte Carlo for rendering. Friedel and Keller ([^30]) described an approach for efficient evaluation of the radical inverse based on reusing some values across multiple sample points. Both the sampling approach based on -sequences that is used in the [PaddedSobolSampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#PaddedSobolSampler) and the algorithm implemented in the [BinaryPermuteScrambler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#BinaryPermuteScrambler) are described in a paper by Kollig and Keller ([^50]). Basu and Owen ([^9]) analyzed the effect of the distortion from warping uniform samples in the context of quasi–Monte Carlo.

The discrepancy bounds for jittered sampling in Equation ([8.17](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Stratified_Sampler.html#eq:jittered-discrepancy)) are due to Pausinger and Steinerberger ([^68]).

\-sequences are one instance of a general type of low-discrepancy sequence known as -sequences and -nets. These were discussed further by Niederreiter ([^65]) and Dick and Pillichshammer ([^20]).

Sobol’ ([^81]) introduced the family of generator matrices used in Section [8.7](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#sec:sobol-sampler). Antonov and Saleev ([^8]) showed that enumerating Sobol’ sample points in Gray code order leads to a highly efficient implementation; see also Bratley and Fox ([^14]) and Wächter’s Ph.D. dissertation ([^89]) for further discussion of high-performance implementation of base-2 generator matrix operations. The Sobol’ generator matrices our implementation uses are enhanced versions derived by Joe and Kuo ([^41]) that improve the 2D projections of sample points. Grünschloß and collaborators found generator matrices for 2D sampling that satisfy the base-2 elementary intervals and are also optimized to improve the sampling pattern’s blue noise properties (Grünschloß et al. [^34], Grünschloß and Keller [^33]).

Braaten and Weller introduced the idea of using digit permutations to improve Halton sample points ([^12]); they used a single permutation for all the digits in a given base, but determined permutations incrementally in order to optimize the -dimensional distribution of points. Better results can be had by using per-digit permutations (as is done in the [DigitPermutation](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Halton_Sampler.html#DigitPermutation) class) and by using carefully constructed deterministic permutations (as is not). Faure ([^28]) described a deterministic approach for computing permutations for scrambled radical inverses; more recently, Faure and Lemieux ([^29]) surveyed a variety of approaches for doing so and proposed a new approach that ensures that the 1- and 2-dimensional projections of scrambled sample points are well distributed.

The nested uniform digit scrambling that has become known as Owen scrambling was introduced by Owen ([^66]), though in its original form it had high storage requirements for the permutations. Tan and Boyle ([^83]) proposed switching to a fixed permutation after some number of digits, and Friedel and Keller ([^30]) cached lazily generated permutations. Owen ([^67]) proposed the hash-based permutation approach that is implemented in both [OwenScrambledRadicalInverse()](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Halton_Sampler.html#OwenScrambledRadicalInverse) and the [OwenScrambler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#OwenScrambler) class. Laine and Karras ([^52]) noted that in base 2, nested uniform digit scrambling could be implemented in parallel across all the digits. The specific function used to do so in the [FastOwenScrambler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#FastOwenScrambler) is due to Vegdahl ([^87]). See also Burley ([^15]) for further discussion of this approach.

The algorithms used for computing sample indices within given pixels in Sections [8.6.3](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Halton_Sampler.html#sec:halton-sampler-implementation) and [8.7.4](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#sec:global-sobol-sampler) were introduced by Grünschloß et al. ([^35]).

*Rank-1 lattices* are a deterministic approach for constructing well-distributed point sets. They were introduced to graphics by Keller ([^46]) and Dammertz and Keller ([2008b](#cite:Dammertz2008b)). More recently, Liu et al. ([^55]) extended them to high-dimensional integration problems in rendering.

As the effectiveness of both low-discrepancy sampling and blue noise has become better understood, a number of researchers have developed sampling techniques that target both metrics. Examples include Ahmed et al. ([^5]), who rearranged low-discrepancy sample points to improve blue noise properties, and Perrier et al. ([^69]), who found Owen scrambling permutations that led to good blue noise characteristics. Heitz et al. ([^38]) started with an Owen-scrambled point set and then improved its blue noise characteristics by solving an optimization problem that sets per-pixel seeds for randomization of the points in a way that decorrelates the error in integrating a set of test functions at nearby pixels. The approach implemented in the [ZSobolSampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sobol_Samplers.html#ZSobolSampler) based on permuted Morton indices to achieve blue noise was introduced by Ahmed and Wonka ([^3]).

An exciting recent development in this area is the recent paper by Ahmed and Wonka ([^4]) that presents algorithms to directly enumerate all the valid digital -nets. In turn, it is possible to apply various optimization algorithms to the generated point sets (e.g., to improve their blue noise characteristics). See also recent work by Helmer et al. ([^39]) for algorithms that incrementally generate sequences of Owen-scrambled Halton, Sobol’, and Faure points, allowing both optimization of the distribution of the points and highly efficient point generation.

#### Filtering and Reconstruction

Cook ([^18]) first introduced the Gaussian filter to graphics. Mitchell and Netravali ([^64]) investigated a family of filters using experiments with human observers to find the most effective ones; the [MitchellFilter](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#MitchellFilter) in this chapter is the one they chose as the best. Kajiya and Ullner ([^43]) investigated image filtering methods that account for the effect of the reconstruction characteristics of Gaussian falloff from pixels in CRTs. Betrisey et al. ([^11]) described Microsoft’s ClearType technology for display of text on LCDs. Alim ([^7]) applied reconstruction techniques that attempt to minimize the error between the reconstructed image and the original continuous image, even in the presence of discontinuities.

There has been quite a bit of research into reconstruction filters for image resampling applications. Although this application is not the same as reconstructing nonuniform samples for image synthesis, much of this experience is applicable. Turkowski ([1990a](#cite:Turkowski90resamp)) reported that the Lanczos windowed sinc filter gives the best results among a number of filters for image resampling. Meijering et al. ([^59]) tested a variety of filters for image resampling by applying a series of transformations to images such that if perfect resampling had been done, the final image would be the same as the original. They also found that the Lanczos window performed well (as did a few others) and that truncating the sinc without a window gave some of the worst results. Other work in this area includes papers by Möller et al. ([^56]) and Machiraju and Yagel ([^57]).

#### Adaptive Sampling and Reconstruction

pbrt does not include samplers that perform adaptive sampling. Though adaptive sampling has been an active area of research, our own experience with the resulting algorithms has been that while most work well in some cases, few are robust across a wide range of scenes. (Adaptive sampling further introduces a coupling between the [Sampler](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Interface.html#Sampler) and the [Film](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film) that we prefer to avoid.)

Early work on adaptive sampling includes that of Lee, Redner, and Uselton ([^53]), who developed a technique for adaptive sampling based on statistical tests that made it possible to compute images to a given error tolerance; Mitchell ([^60]), who investigated the use of contrast differences for adaptive sampling; and Purgathofer ([^73]), who applied statistical tests. Kajiya applied adaptive sampling to the Monte Carlo light transport integral ([^42]).

Mitchell ([^60]) observed that standard image reconstruction techniques fail in the presence of adaptive sampling: the contribution of a dense clump of samples in part of the filter’s extent may incorrectly have a large effect on the final value purely due to the number of samples taken in that region. He described a multi-stage box filter that addresses this issue. Kirk and Arvo ([^49]) identified a subtle problem with adaptive sampling algorithms: in short, if a set of samples is not only used to decide if more samples should be taken but is also added to the image, bias is introduced.

Zwicker et al.’s survey article ([^92]) includes a thorough summary of work in adaptive sampling through 2015. More recently, Ahmed et al. ([^6]) described an approach for generating adaptive samples that maintains good blue noise properties. Vogels et al. ([^88]), Kuznetsov et al. ([^51]), and Hasselgren et al. ([^36]) have all trained neural nets to determine where additional samples should be taken in a noisy image.

Much recent work on adaptive sampling has been based on the foundation of Durand et al.’s ([^25]) frequency analysis of light transport. Much of it not only adapts sampling based on frequency space insights but also applies filters that are tailored to the frequency content of the functions being sampled. Shinya ([^77]) and Egan et al. ([^26]) developed adaptive sampling and reconstruction methods focused on rendering motion blur. Belcour et al. ([^10]) computed 5D covariance of image, time, and lens defocus and applied adaptive sampling and high-quality reconstruction. While most earlier work focused on single effects—soft shadows, motion blur, etc.—Wu et al. ([^90]) showed how to efficiently filter according to multiple such effects at once.

### References

[^1]: Öztireli, A. C. 2016. Integration with stochastic point processes. *ACM Transactions on Graphics* *35*  (5), 160:1–16.

[^2]: Öztireli, A. C. 2020. A comprehensive theory and variational framework for anti-aliasing sampling patterns. *Computer Graphics Forum* *39*  (4), 133–48.

[^3]: Ahmed, A. G. M., and P. Wonka. 2020. Screen-space blue-noise diffusion of Monte Carlo sampling error via hierarchical ordering of pixels. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *39*  (6), 244:1–15.

[^4]: Ahmed, A. G. M., and P. Wonka. 2021. Optimizing dyadic nets. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 141:1–17.

[^5]: Ahmed, A., H. Perrier, D. Coeurjolly, V. Ostromoukhov, J. Guo, D. Yan, H. Huang, and O. Deussen. 2016. Low-discrepancy blue noise sampling. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *35*  (6), 247:1–13.

[^6]: Ahmed, A., T. Niese, H. Huang, and O. Deussen. 2017. An adaptive point sampler on a regular lattice. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 138:1–13.

[^7]: Alim, U. R. 2013. Rendering in shift-invariant spaces. In *Proceedings of Graphics Interface 2013*, 189–96.

[^8]: Antonov, I. A., and V. M. Saleev. 1979. An economic method of computing LP sequences. *Zh. Vychisl. Mat. Mat. Fiz.* *19*  (1), 243–45. (*U.S.S.R. Computational Mathematics and Mathematical Physics* *19*  (1), 252–56.)

[^9]: Basu, K., and A. B. Owen. 2016. Transformations and Hardy–Krause variation. *SIAM Journal on Numerical Analysis* *54*  (3), 1946–66.

[^10]: Belcour, L., C. Soler, K. Subr, N. Holzschuch, and F. Durand. 2013. 5D covariance tracing for efficient defocus and motion blur. *ACM Transactions on Graphics* *32*  (3), 31:1–18.

[^11]: Betrisey, C., J. F. Blinn, B. Dresevic, B. Hill, G. Hitchcock, B. Keely, D. P. Mitchell, J. C. Platt, and T. Whitted. 2000. Displaced filtering for patterned displays. *Society for Information Display International Symposium. Digest of Technical Papers* *31*, 296–99.

[^12]: Braaten, E., and G. Weller. 1979. An improved low-discrepancy sequence for multidimensional quasi-Monte Carlo integration. *Journal of Computational Physics* *33*  (2), 249–58.

[^13]: Bracewell, R. N. 2000. *The Fourier Transform and Its Applications*. New York: McGraw-Hill.

[^14]: Bratley, P., and B. L. Fox. 1988. Algorithm 659: Implementing Sobol’s quasirandom sequence generator. *ACM Transactions on Mathematical Software* *14*  (1), 88–100.

[^15]: Burley, B. 2020. Hash-based Owen scrambling. *Journal of Computer Graphics Techniques (JCGT)* *9*  (4), 1–20.

[^16]: Chiu, K., P. Shirley, and C. Wang. 1994. Multi-jittered sampling. In P. Heckbert (ed.), *Graphics Gems IV*, 370–74. San Diego: Academic Press.

[^17]: Christensen, P., A. Kensler, and C. Kilpatrick. 2018. Progressive multi-jittered sample sequences. *Computer Graphics Forum* *37*  (4), 21–33.

[^18]: Cook, R. L. 1986. Stochastic sampling in computer graphics. *ACM Transactions on Graphics* *5*  (1), 51–72.

[^19]: Crow, F. C. 1977. The aliasing problem in computer-generated shaded images. *Communications of the ACM* *20*  (11), 799–805.

[^20]: Dick, J., and F. Pillichshammer. 2010. *Digital Nets and Sequences: Discrepancy Theory and Quasi-Monte Carlo Integration*. Cambridge: Cambridge University Press.

[^21]: Dippé, M. A. Z., and E. H. Wold. 1985. Antialiasing through stochastic sampling. *Computer Graphics (SIGGRAPH ’85 Proceedings)* *19*, 69–78.

[^22]: Dobkin, D. P., and D. P. Mitchell. 1993. Random-edge discrepancy of supersampling patterns. In *Proceedings of Graphics Interface 1993*, Toronto, Ontario, 62–69. Canadian Information Processing Society.

[^23]: Dobkin, D. P., D. Eppstein, and D. P. Mitchell. 1996. Computing the discrepancy with applications to supersampling patterns. *ACM Transactions on Graphics* *15*  (4), 354–76.

[^24]: Durand, F. 2011. A frequency analysis of Monte-Carlo and other numerical integration schemes. *MIT CSAIL Technical Report 2011-052*

[^25]: Durand, F., N. Holzschuch, C. Soler, E. Chan, and F. X. Sillion. A frequency analysis of light transport. 2005. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2005)* *24*  (3), 1115–26.

[^26]: Egan, K., Y.-T. Tseng, N. Holzschuch, F. Durand, and R. Ramamoorthi. 2009. Frequency analysis and sheared reconstruction for rendering motion blur. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2009)* *28*  (3), 93:1–13.

[^27]: Eldar, Y. C., and T. Michaeli. 2009. Beyond bandlimited sampling. *IEEE Signal Processing Magazine* *26*  (3), 48–68.

[^28]: Faure, H. 1992. Good permutations for extreme discrepancy. *Journal of Number Theory* *42*  (1), 47–56.

[^29]: Faure, H., and C. Lemieux. 2009. Generalized Halton sequences in 2008: A comparative study. *ACM Transactions on Modeling and Computer Simulation* *19*  (4), 15:1–31.

[^30]: Friedel, I., and A. Keller. 2002. Fast generation of randomized low-discrepancy point sets. *Monte Carlo and Quasi–Monte Carlo Methods 2000*, 257–73.

[^31]: Georgiev, I., and M. Fajardo. 2016. Blue-noise dithered sampling. *ACM SIGGRAPH 2016 Talks (SIGGRAPH ’16)* 35:1.

[^32]: Glassner, A. 1995. *Principles of Digital Image Synthesis*. San Francisco: Morgan Kaufmann.

[^33]: Grünschloß, L., and A. Keller. 2009. (t, m, s)-nets and maximized minimum distance, part II. In *Monte Carlo and Quasi-Monte Carlo Methods 2008*, 395–409. Berlin: Springer Verlag.

[^34]: Grünschloß, L., J. Hanika, R. Schwede, and A. Keller. 2008. (t, m, s)-nets and maximized minimum distance. In *Monte Carlo and Quasi-Monte Carlo Methods 2006*, 397–412. Berlin: Springer Verlag.

[^35]: Grünschloß, L., M. Raab, and A. Keller. 2012. Enumerating quasi-Monte Carlo point sequences in elementary intervals. In *Monte Carlo and Quasi-Monte Carlo Methods 2010*, 399–408. Berlin: Springer Verlag.

[^36]: Hasselgren, J., J. Munkberg, A. Patney, M. Salvi, and A. Lefohn. 2020. Neural temporal adaptive sampling and denoising. *Computer Graphics Forum* *39*  (2), 147–55.

[^37]: Heitz, E., and L. Belcour. 2019. Distributing Monte Carlo errors as a blue noise in screen space by permuting pixel seeds between frames. *Computer Graphics Forum* *38*  (4), 149–58.

[^38]: Heitz, E., L. Belcour, V. Ostromoukhov, D. Coeurjolly, and J.-C. Iehl. 2019. A low-discrepancy sampler that distributes Monte Carlo errors as a blue noise in screen space. *SIGGRAPH ’19 Talks*, 68:1–2.

[^39]: Helmer, A., P. Christensen, and A. Kensler. 2021. Stochastic generation of (t,s) sample sequences. *Proceedings of the Eurographics Symposium on Rendering*, 21–33.

[^40]: Jarosz, W., A. Enayet, A. Kensler, C. Kilpatrick, and P. Christensen. 2019. Orthogonal array sampling for Monte Carlo rendering. *Computer Graphics Forum* *38*  (4), 135–47.

[^41]: Joe, S., and F.-Y. Kuo. 2008. Constructing Sobol sequences with better two-dimensional projections. *SIAM J. Sci. Comput.* *30*, 2635–54.

[^42]: Kajiya, J. T. 1986. The rendering equation. In *Computer Graphics (SIGGRAPH ’86 Proceedings)* *20*, 143–50.

[^43]: Kajiya, J., and M. Ullner. 1981. Filtering high quality text for display on raster scan devices. In *Computer Graphics (Proceedings of SIGGRAPH ’81)*, 7–15.

[^44]: Keller, A. 1997. Instant radiosity. In *Proceedings of SIGGRAPH ’97*, Computer Graphics Proceedings, Annual Conference Series, 49–56.

[^45]: Keller, A. 2001. Strictly deterministic sampling methods in computer graphics. *mental images Technical Report.* Also in *SIGGRAPH 2003 Monte Carlo Course Notes.*

[^46]: Keller, A. 2004. Stratification by rank-1 lattices. *Monte Carlo and Quasi-Monte Carlo Methods 2002*, 299–313. Berlin: Springer-Verlag.

[^47]: Keller, A. 2012. Quasi-Monte Carlo image synthesis in a nutshell. In *Monte Carlo and Quasi-Monte Carlo Methods 2012*, 213–49. Berlin: Springer-Verlag.

[^48]: Kensler, A. 2013. Correlated multi-jittered sampling. *Pixar Technical Memo 13-01*

[^49]: Kirk, D. B., and J. Arvo. 1991. Unbiased sampling techniques for image synthesis. *Computer Graphics (SIGGRAPH ’91 Proceedings)*, Volume 25, 153–56.

[^50]: Kollig, T., and A. Keller. 2002. Efficient multidimensional sampling. *Computer Graphics Forum (Proceedings of Eurographics 2002)*, Volume 21, 557–63.

[^51]: Kuznetsov, A., N. K. Kalantari, and R. Ramamoorthi. 2018. Deep adaptive sampling for low sample count rendering. *Computer Graphics Forum* *37*  (4), 35–44.

[^52]: Laine, S., and T. Karras. 2011. Stratified sampling for stochastic transparency. In *Computer Graphics Forum* *30*  (4), 1197–204.

[^53]: Lee, M. E., R. A. Redner, and S. P. Uselton. 1985. Statistically optimized sampling for distributed ray tracing. In *Computer Graphics (Proceedings of SIGGRAPH ’85)*, Volume 19, 61–67.

[^54]: Lessig, C., M. Desbrun, and E. Fiume. 2014. A constructive theory of sampling for image synthesis using reproducing kernel bases. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2014)* *33*  (4), 55:1–14.

[^55]: Liu, H., H. Han, and M. Jiang. 2021. Rank-1 lattices for efficient path integral estimation. *Computer Graphics Forum (Proceedings of Eurographics)* *40*  (2), 91–102.

[^56]: Möller, T., R. Machiraju, K. Mueller, and R. Yagel. 1997. Evaluation and design of filters using a Taylor series expansion. *IEEE Transactions on Visualization and Computer Graphics* *3*  (2), 184–99.

[^57]: Machiraju, R., and R. Yagel. 1996. Reconstruction error characterization and control: A sampling theory approach. *IEEE Transactions on Visualization and Computer Graphics* *2*  (4), 364–78.

[^58]: Meijering, E. 2002. A chronology of interpolation: From ancient astronomy to modern signal and image processing. In *Proceedings of the IEEE* *90*  (3), 319–42.

[^59]: Meijering, E. H. W., W. J. Niessen, J. P. W. Pluim, and M. A. Viergever. 1999. Quantitative comparison of sinc-approximating kernels for medical image interpolation. *Medical Image Computing and Computer-Assisted Intervention—MICCAI 1999*, 210–17.

[^60]: Mitchell, D. P. 1987. Generating antialiased images at low sampling densities. *Computer Graphics (SIGGRAPH ’87 Proceedings)*, Volume 21, 65–72.

[^61]: Mitchell, D. P. 1991. Spectrally optimal sampling for distributed ray tracing. *Computer Graphics (SIGGRAPH ’91 Proceedings)*, Volume 25, 157–64.

[^62]: Mitchell, D. P. 1992. Ray tracing and irregularities of distribution. In *Third Eurographics Workshop on Rendering*, 61–69.

[^63]: Mitchell, D. P. 1996. Consequences of stratified sampling in graphics. In *Proceedings of SIGGRAPH ’96*, Computer Graphics Proceedings, Annual Conference Series, 277–80.

[^64]: Mitchell, D. P., and A. N. Netravali. 1988. Reconstruction filters in computer graphics. *Computer Graphics (SIGGRAPH ’88 Proceedings)*, Volume 22, 221–28.

[^65]: Niederreiter, H. 1992. *Random Number Generation and Quasi–Monte Carlo Methods*. Philadelphia: Society for Industrial and Applied Mathematics.

[^66]: Owen, A. B. 1995. Randomly permuted (t, m, s)-nets and (t, s)-sequences. In *Monte Carlo and Quasi-Monte Carlo Methods in Scientific Computing*, 299–317.

[^67]: Owen, A. B. 2003. Variance with alternative scramblings of digital nets. *ACM Transactions on Modeling and Computer Simulation* *13*  (4), 363–78.

[^68]: Pausinger, F., and S. Steinerberger. 2016. On the discrepancy of jittered sampling. *Journal of Complexity* *33*, 199–216.

[^69]: Perrier, H., D. Coeurjolly, F. Xie, M. Pharr, P. Hanrahan, and V. Ostromoukhov. 2018. Sequences with low-discrepancy blue-noise 2-D projections. *Computer Graphics Forum* *37*  (2), 339–53.

[^70]: Peters, C. 2016. Free blue noise textures. [http://momentsingraphics.de/BlueNoise.html](http://momentsingraphics.de/BlueNoise.html)

[^71]: Pharr, M. 2019. Efficient generation of points that satisfy two-dimensional elementary intervals. *Journal of Computer Graphics Techniques (JCGT)* *8*  (1), 56–68.

[^72]: Pilleboue, A., G. Singh, D. Coeurjolly, M. Kazhdan, and V. Ostromoukhov. 2015. Variance analysis for Monte Carlo integration. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2015)* *34*  (4), 124:1–14.

[^73]: Purgathofer, W. 1987. A statistical mothod for adaptive stochastic sampling. *Computers & Graphics* *11*  (2), 157–62.

[^74]: Ramamoorthi, R., and P. Hanrahan. 2004. A signal-processing framework for reflection. *ACM Trans. Graph. 23*  (4), 1004–42.

[^75]: Reinert, B., T. Ritschel, H.-P. Seidel, and I. Georgiev. 2015. Projective blue-noise sampling. *Computer Graphics Forum* *35*  (1), 285–95.

[^76]: Sen, P., and S. Darabi. 2011. Compressive rendering: A rendering application of compressed sensing. *IEEE Transactions on Visualization and Computer Graphics* *17*  (4), 487–99.

[^77]: Shinya, M. 1993. Spatial anti-aliasing for animation sequences with spatio-temporal filtering. In *Proceedings of SIGGRAPH ’93*, Computer Graphics Proceedings, Annual Conference Series, 289–96.

[^78]: Shirley, P. 1991. Discrepancy as a quality measure for sample distributions. *Eurographics ’91*, 183–94.

[^79]: Singh, G., and W. Jarosz. 2017. Convergence analysis for anisotropic Monte Carlo sampling spectra. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 137:1–14.

[^80]: Singh, G., B. Miller, and W. Jarosz. 2017. Variance and convergence analysis of Monte Carlo line and segment sampling. *Computer Graphics Forum* *36*  (4), 79–89.

[^81]: Sobol, I. 1967. On the distribution of points in a cube and the approximate evaluation of integrals. *Zh. vychisl. Mat. mat. Fiz.* *7*  (4), 784–802.

[^82]: Subr, K., and J. Kautz. 2013. Fourier analysis of stochastic sampling strategies for assessing bias and variance in integration. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2013)* *32*  (4), 128:1–12.

[^83]: Tan, K. S., and P. P. Boyle. 2000. Applications of randomized low discrepancy sequences to the valuation of complex securities. *Journal of Economic Dynamics and Control* *24*, 1747–82.

[^84]: Ulichney, R. A. 1988. Dithering with blue noise. *Proceedings of the IEEE* *76*  (1), 56–79.

[^85]: Ulichney, R. A. 1993. Void-and-cluster method for dither array generation. *Proc. SPIE 1913, Human Vision, Visual Processing, and Digital Display IV*

[^86]: Unser, M. 2000. Sampling—50 years after Shannon. In *Proceedings of the IEEE* *88*  (4), 569–87.

[^87]: Vegdahl, N. 2021. Building a better LK hash. [https://psychopath.io/post/2021\_01\_30\_building\_a\_better\_lk\_hash](https://psychopath.io/post/2021_01_30_building_a_better_lk_hash)

[^88]: Vogels, T., F. Rousselle, B. McWilliams, G. Röthlin, A. Harvill, D. Adler, M. Meyer, and J. Novák. 2018. Denoising with kernel prediction and asymmetric loss functions. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *37*  (4), 124:1–15.

[^89]: Wächter, C. A. 2008. Quasi Monte Carlo light transport simulation by efficient ray tracing. Ph.D. thesis, University of Ulm.

[^90]: Wu, L., L.-Q. Yan, A. Kuznetsov, and R. Ramamoorthi. 2017. Multiple axis-aligned filters for rendering of combined distribution effects. *Computer Graphics Forum* *36*  (4), 155–66.

[^91]: Yellot, J. I. 1983. Spectral consequences of photoreceptor sampling in the Rhesus retina. *Science* *221*, 382–85.

[^92]: Zwicker, M., W. Jarosz, J. Lehtinen, B. Moon, R. Ramamoorthi, F. Rousselle, P. Sen, C. Soler, and S.-E. Yoon. 2015. Recent advances in adaptive sampling and reconstruction for Monte Carlo rendering. *Computer Graphics Forum (Proceedings of Eurographics 2015)* *34*  (2), 667–81.