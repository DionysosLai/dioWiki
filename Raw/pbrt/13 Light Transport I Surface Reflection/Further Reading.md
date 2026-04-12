---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
The first application of Monte Carlo to global illumination for creating synthetic images that we are aware of was described in Tregenza’s paper on lighting design ([Tregenza 1983](#cite:Tregenza83)). Cook’s distribution ray-tracing algorithm computed glossy reflections, soft shadows from area lights, motion blur, and depth of field with Monte Carlo sampling ([Cook et al. 1984](#cite:Cook84); [Cook 1986](#cite:Cook86)), although the general form of the light transport equation was not stated until papers by Kajiya ([^42]) and Immel, Cohen, and Greenberg ([^34]).

Kajiya ([^42]) introduced the general-purpose path-tracing algorithm. Other important early work on Monte Carlo in rendering includes Shirley’s Ph.D. thesis ([^74]) and a paper by Kirk and Arvo ([^47]) on sources of bias in rendering algorithms.

Fundamental theoretical work on light transport has been done by Arvo ([^2], [1995a](#cite:ArvoThesis)), who investigated the connection between rendering algorithms in graphics and previous work in *transport theory*, which applies classical physics to particles and their interactions to predict their overall behavior. Our description of the path integral form of the LTE follows the framework in Veach’s Ph.D. thesis, which has thorough coverage of different forms of the LTE and its mathematical structure ([Veach 1997](#cite:VeachThesis)).

The next event estimation technique that corresponds to the direct lighting computation in path tracing was first introduced by Coveyou et al. ([^15]), in the context of neutron transport.

Russian roulette was introduced to graphics by Arvo and Kirk ([^3]). Hall and Greenberg ([^27]) had previously suggested adaptively terminating ray trees by not tracing rays with less than some minimum contribution. Arvo and Kirk’s technique is unbiased, although in some situations bias and less noise may be the more desirable artifact.

The Russian roulette termination probability computed in the [PathIntegrator](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/A_Better_Path_Tracer.html#PathIntegrator) is largely determined by the albedo of the surface at the last scattering event; that approach was first introduced by Szesci et al. ([^77]). This is a reasonable way to set the probability, but better is to set the termination probability that also accounts for the incident lighting at a point: it would be better to terminate paths more aggressively in darker parts of the scene and less aggressively in brighter parts. Vorba and Křivánek described an approach for doing so based on an approximation of the lighting in the scene ([Vorba and Křivánek 2016](#cite:Vorba2016)). They further applied splitting to the problem, increasing the number of paths in important regions.

*Control variates* is a Monte Carlo technique based on finding an approximation to the integrand that is efficient to evaluate and then applying Monte Carlo to integrate the difference between the approximation and the true integrand. The variance of the resulting estimator then is dependent on the difference. This approach was first applied to rendering by Lafortune and Willems ([^50], [^51]). Recent work in this area includes Rousselle et al. ([^70]), who made use of correlations between nearby pixels to define control variates. (Their paper also has comprehensive coverage of other applications of control variates to rendering after Lafortune and Willems’s work.) Müller et al. ([^54]) have demonstrated the effectiveness of neural networks for computing control variates for rendering. Crespo et al. ([^16]) fit polynomials to the samples taken in each pixel and used them as control variates, showing reduction in error in pixels where the integrand was smooth.

One approach to improving the performance of path tracing is to reuse computation across nearby points in the scene. Irradiance caching ([Ward et al. 1988](#cite:Ward88); [Ward 1994](#cite:Ward94)) is one such technique. It is based on storing the irradiance due to indirect illumination at a sparse set of points on surfaces in the scene; because indirect lighting is generally slowly changing, irradiance can often be safely interpolated. Tabellion and Lamorlette ([^78]) described a number of additional improvements to irradiance caching that made it viable for rendering for movie productions.

Křivánek and collaborators generalized irradiance caching to *radiance caching*, where a more complex directional distribution of incident radiance is stored, so that more accurate shading from glossy surfaces is possible (Křivánek et al. [^41]). Schwarzhaupt et al. have proposed a better way of assessing the validity of a cache point using a second-order expansion of the incident lighting (Schwarzhaupt et al. [^73]) and Zhao et al. ([^92]) have developed a number of improvements that are especially useful for glossy scenes. Ren et al. ([^68]) first applied neural networks to represent the radiance distribution in a scene for rendering; more recently, Müller et al. ([^55]) trained a fully connected 7-layer network to represent radiance during rendering and demonstrated both high performance and accurate indirect illumination.

#### Improved Estimators and Sampling Algorithms

A number of approaches have been developed to sample from the product distribution of the BSDF and light source for direct lighting (Burke et al. [^9]; Cline et al. [^13]). Product sampling can give better results than MIS-weighted light and BSDF samples when neither of those distributions matches the true product well. Clarberg, Rousselle, and collaborators developed techniques based on representing BSDFs and illumination in the wavelet basis and efficiently sampling from their product (Clarberg et al. [^11]; Rousselle et al. [^69]; Clarberg and Akenine-Möller [2008a](#cite:Clarberg08practical)). Efficiency of the direct lighting calculation can be further improved by sampling from the *triple product* distribution of BSDF, illumination, and visibility; this issue was investigated by Ghosh and Heidrich ([^22]) and Clarberg and Akenine-Möller ([2008b](#cite:Clarberg2008b)). Wang and Åkerlund ([^88]) introduced an approximation to the indirect illumination that is used in the light sampling distribution with these approaches. More recently, Belcour et al. ([^5]) derived approaches for integrating the spherical harmonics over polygonal domains and demonstrated their application to product sampling. Hart et al. ([^28]) showed how simple warps of uniform random samples can be used for product sampling. Peters ([2021b](#cite:Peters2021polygonal)) has shown use of linearly transformed cosines ([Heitz et al. 2016a](#cite:Heitz2016)) with a new algorithm for sampling polygonal light sources to perform product sampling.

Subr et al. ([^75]) analyzed the combination of multiple importance sampling and jittered sampling for direct lighting calculations and proposed techniques that improve convergence rates.

Heitz et al. ([^29]) applied ratio estimators to direct illumination computations, which allows the use of analytic techniques for computing unshadowed direct illumination and then computing the correct result in expectation after tracing a shadow ray. They showed the effectiveness of this approach with sophisticated models for analytic illumination from area lights ([Heitz et al. 2016a](#cite:Heitz2016); [Dupuy et al. 2017](#cite:Dupuy2017)) and noted a number of benefits of this formulation in comparison to control variates. Another approach for applying analytic techniques to direct lighting was described by Billen and Dutré ([^7]) and Salesin and Jarosz ([^72]), who integrated one dimension of the integral analytically.

Path regularization was introduced by Kaplanyan and Dachsbacher ([^43]). Our implementation applies an admittedly *ad hoc* roughening to all non-diffuse BSDFs, while they only applied regularization to Dirac delta distributions and replaced them with a function designed to not lose energy, as ours may. See also Bouchard et al. ([^8]), who incorporated regularization as one of the sampling strategies to use with MIS. A principled approach to regularization for microfacet-based BSDFs was developed by Jendersie and Grosch ([^36]). Weier et al. ([^89]) have recently developed a path regularization approach based on learning regularization parameters with a variety of scenes and differentiable rendering.

A number of specialized sampling techniques have been developed for especially tricky scattering problems. Wang et al. ([2020b](#cite:Wang2020)) developed methods to render scattering paths that exclusively exhibit specular light transport, including those that start at pinhole cameras and end at point light sources. Such light-carrying paths cannot be sampled directly using the incremental path sampling approach used in pbrt. Loubet et al. ([^53]) showed how to efficiently render caustics in a path tracer by constructing a data structure that records which triangles may cast caustics in a region of space and then directly sampling a specular light path from the light to the triangle to a receiving point. Zeltner et al. ([^91]) found caustic paths using a equation-solving iteration with random initialization, which requires precautions when reasoning about the probability of a generated sample.

#### Path Guiding

The [PathIntegrator](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/A_Better_Path_Tracer.html#PathIntegrator) samples the BSDF in order to sample indirect illumination, though for scenes where the indirect illumination varies significantly as a function of direction, this is not an ideal approach. A family of approaches that have come to be known as *path guiding* have been developed to address this problem; all share the idea of building a data structure that represents the indirect illumination in the scene and then using it to draw samples. Early work in this area was done by Lafortune and Willems ([^51]), who used a 5D tree to represent the scene radiance distribution, and Jensen ([^37]), who traced samples from the light sources (“photons”) and used them to do the same. Hey and Purgathofer ([2002a](#cite:Hey2002)) developed an improved approach based on photons and Pegoraro et al. ([2008a](#cite:Pegoraro2008a)) applied the theory of sequential Monte Carlo to this problem. An early path guiding technique based on adapting the distribution of uniform random samples to better sample important paths was described by Cline et al. ([^12]).

Vorba et al. ([^82]) applied a parametric representation based on Gaussian mixture models (GMMs) that are learned over the course of rendering for path guiding and Herholz et al. ([^30]) also included the BRDF in GMMs, demonstrating better performance in scenes with non-diffuse BSDFs. Ruppert et al. ([^71]) described a number of further improvements, applying the von Mises–Fisher distribution for their parametric model, improving the robustness of the fitting algorithm, and accounting for parallax, which causes the directional distribution of incident radiance to vary over volumes of space.

A path guiding technique developed by Müller and collaborators ([Müller et al. 2017](#cite:Muller2017); [Müller 2019](#cite:Muller2019:ppg-notes)) has seen recent adoption. It is based on an adaptive spatial decomposition using an octree where each octree leaf node stores an adaptive directional decomposition of incident radiance. Both of these decompositions are refined as more samples are taken and are used for sampling ray directions. This approach was generalized to include product sampling with the BSDF by Diolatzis et al. ([^19]), who used Heitz et al.’s ([2016a](#cite:Heitz2016)) linearly transformed cosines representation to do so.

A challenge with path guiding is that the Monte Carlo estimator generally includes variance due to factors not accounted for by the path guiding algorithm. Rath et al. ([^66]) considered this issue and developed an approach for accounting for this variance in the function that is learned for guiding.

Reibold et al. ([^67]) described a path guiding method based on storing entire ray paths and then defining a PDF for path guiding using Gaussian distributions around them in path space.

Machine learning approaches have also been applied to path guiding: Dahm and Keller ([^18]) investigated the connections between light transport and reinforcement learning and Müller et al. and Zheng and Zwicker both used neural nets to learn the illumination in the scene and applied them to importance sampling ([Müller et al. 2019](#cite:Muller2019:nis); [Zheng and Zwicker 2019](#cite:Zheng2019)). A scene-independent approach was described by Bako et al. ([^4]), who trained a neural net to take a local neighborhood of sample values and reconstruct the incident radiance function to use for path guiding. Deep reinforcement learning has been applied to this problem by Huo et al. ([^33]). Zhu et al. ([^93]) recently introduced a path guiding approach based on storing directional samples in a quadtree and applying a neural network to generate sampling distributions from such quadtrees. They further generated quadtree samples using paths both from the camera and from the light sources and showed that doing so further reduces error in challenging lighting scenarios.

#### Photon Mapping

The general idea of tracing light-carrying paths from light sources was first investigated by Arvo ([^1]), who stored light in texture maps on surfaces and rendered caustics. Heckbert ([1990b](#cite:Heckbert90)) built on this approach to develop a general ray-tracing-based global illumination algorithm, and Dutré et al. ([^20]) and Pattanaik and Mudur ([^62]) developed early particle-tracing techniques. Christensen ([^10]) surveyed applications of adjoint functions and importance to solving the LTE and related problems.

Jensen ([^37]) developed the photon mapping algorithm, which introduced the key innovation of storing light contributions in a general 3D data structure. Important early improvements to the photon mapping method are described in follow-up papers and a book by Jensen ([^38], [^39], [^40]).

Herzog et al. ([^31]) described an approach based on storing all the visible points as seen from the camera and splatting photon contributions to them. Hachisuka et al. ([^26]) developed the progressive photon mapping algorithm, which builds on that representation; stochastic progressive photon mapping (SPPM) was subsequently developed by Hachisuka and Jensen ([^24]). (The online edition of this book includes an implementation of the SPPM algorithm.)

The question of how to find the most effective set of photons for photon mapping is an important one: light-driven particle-tracing algorithms do not work well for all scenes (consider, for example, a complex building model with lights in every room but where the camera sees only a single room). Recent techniques for improved photon sampling include the work of Grittmann et al., who adapted the primary sample space distribution of samples in order to more effectively generate photon paths ([Grittmann et al. 2018](#cite:Grittmann2018)). Conty Estevez and Kulla described an adaptive photon shooting algorithm that has been used in production ([^14]). Both papers survey previous work in that area.

#### Bidirectional Path Tracing

Bidirectional path tracing constructs paths starting both from the camera and from the lights and then forms connections between them. Doing so can be an effective way to sample some light-carrying paths. This technique was independently developed by Lafortune and Willems ([^49]) and Veach and Guibas ([^79]). The development of multiple importance sampling was integral to the effectiveness of bidirectional path tracing (Veach and Guibas [^80]). Lafortune and Willems ([^48]) showed how to apply bidirectional path tracing to rendering participating media. (An implementation of bidirectional path tracing is included in the online edition of the book; many additional references to related work are included there.)

Simultaneous work by Hachisuka et al. ([^25]) and Georgiev et al. ([^21]) provided a unified framework for both photon mapping and bidirectional path tracing. (This approach is often called either *unified path sampling* (UPS) or *vertex connection and merging* (VCM), after respective terminology in those two papers.) Their approaches allowed photon mapping to be included in the path space formulation of the light transport equation, which in turn made it possible to derive light transport algorithms that use both approaches to generate paths and combine them using multiple importance sampling.

#### Metropolis Sampling

Veach and Guibas ([^81]) first applied the Metropolis sampling algorithm to solving the light transport equation. They demonstrated how this method could be applied to image synthesis and showed that the result was a light transport algorithm that was robust to traditionally difficult lighting configurations (e.g., light shining through a slightly ajar door). Pauly, Kollig, and Keller ([^64]) generalized the Metropolis light transport (MLT) algorithm to include volume scattering. Pauly’s thesis (Pauly [^63]) described the theory and implementation of bidirectional and Metropolis-based algorithms for volume light transport.

MLT algorithms generally are unable to take advantage of the superior convergence rates offered by well-distributed sample values. Bitterli and Jarosz present a hybrid light transport algorithm that uses path tracing by default but with the integrand partitioned so that only high-variance samples are handled instead by Metropolis sampling ([Bitterli and Jarosz 2019](#cite:Bitterli2019)). In this way, the benefits of both algorithms are available, with Metropolis available to sample tricky paths and path tracing with well-distributed sample points efficiently taking care of the rest.

Kelemen et al. ([^45]) developed the “primary sample space MLT” formulation of Metropolis light transport, which is much easier to implement than Veach and Guibas’s original formulation. That approach is implemented in the online edition of this book, including the “multiplexed MLT” improvement developed by Hachisuka et al. ([^23]).

Inverting the sampling functions that convert primary sample space samples to light paths makes it possible to develop MLT algorithms that operate both in primary sample space and in path space, the basis of Veach and Guibas’s original formulation of MLT. Pantaleoni ([^61]) used such inverses to improve the distribution of samples and to develop new light transport algorithms, and Otsu et al. ([^60]) developed a novel approach that applies mutations in both spaces. Bitterli et al. ([2018a](#cite:Bitterli2018:rjmlt)) used this approach to apply reversible jump Markov chain Monte Carlo to light transport and to develop new sampling techniques.

See Šik and Křivánek’s article for a comprehensive survey of the application of Markov chain sampling algorithms to light transport ([Šik and Křivánek 2018](#cite:Sik2018)).

#### Other Rendering Approaches

A number of algorithms have been developed based on a first phase of computation that traces paths from the light sources to create so-called virtual lights, where these lights are then used to approximate indirect illumination during a second phase. The principles behind this approach were first introduced by Keller’s work on *instant radiosity* ([Keller 1997](#cite:Keller97)). The more general *instant global illumination* algorithm was developed by Wald, Benthin, and collaborators (Wald et al. [^84], [^83]; Benthin et al. [^6]). See Dachsbacher et al.’s survey ([^17]) for a summary of work in this area.

Building on the virtual point lights concept, Walter and collaborators ([^87], [^85]) developed *lightcuts*, which are based on creating thousands of virtual point lights and then building a hierarchy by progressively clustering nearby ones together. When a point is being shaded, traversal of the light hierarchy is performed by computing bounds on the error that would result from using clustered values to illuminate the point versus continuing down the hierarchy, leading to an approach with both guaranteed error bounds and good efficiency. A similar hierarchy is used by Yuksel and Yuksel ([^90]) for determining the illumination from volumetric emitters. Bidirectional lightcuts (Walter et al. [^86]) trace longer subpaths from the camera to obtain a family of light connection strategies; combining the strategies using multiple importance sampling eliminates bias artifacts that are commonly produced by virtual point light methods.

Jakob and Marschner ([^35]) expressed light transport involving specular materials as an integral over a high-dimensional manifold embedded in path space. A single light path corresponds to a point on the manifold, and nearby paths are found using a local parameterization that resembles Newton’s method; they applied a Metropolis-type method through this parameterization to explore the neighborhood of challenging specular and near-specular configurations.

Hanika et al. ([2015a](#cite:Hanika2015a)) applied an improved version of the local path parameterization in a pure Monte Carlo context to estimate the direct illumination through one or more dielectric boundaries; this leads to significantly better convergence when rendering glass-enclosed objects or surfaces covered with water droplets.

Kaplanyan et al. ([^44]) observed that the path contribution function is close to being separable when paths are parameterized using the endpoints and the half-direction vectors at intermediate vertices, which are equal to the microfacet normals in the context of microfacet reflectance models. Performing Metropolis sampling in this half-vector domain leads to a method that is particularly good at rendering glossy interreflection. An extension by Hanika et al. ([2015b](#cite:Hanika2015b)) improves the robustness of this approach and proposes an optimized scheme to select mutation sizes to reduce sample clumping in image space.

Another interesting approach was developed by Lehtinen and collaborators, who considered rendering from the perspective of computing gradients of the image (Lehtinen et al. [^52], Manzi et al. [^56]). Their insight was that, ideally, most samples from the path space should be taken around discontinuities and not in smooth regions of the image. They then developed a measurement contribution function for Metropolis sampling that focused samples on gradients and then reconstructed high-quality final images from horizontal and vertical gradient images and a coarse, noisy image. More recently, Kettunen et al. ([^46]) showed how this approach could be applied to regular path tracing without Metropolis sampling. Manzi et al. ([^57]) showed its application to bidirectional path tracing and Sun et al. ([^76]) applied it to vertex connection and merging. Petitjean et al. ([^65]) used gradient domain techniques to improve spectral rendering. Hua et al. ([^32]) have written a comprehensive survey of work in this area.

Hair is particularly challenging to render; not only is it extremely geometrically complex but multiple scattering among hair also makes a significant contribution to its final appearance. Traditional light transport algorithms often have difficulty handling this case well. See the papers by Moon and Marschner ([^58]), Moon et al. ([^59]), and Zinke et al. ([^94]) for recent work in specialized rendering algorithms for hair. Yan et al. ([2017b](#cite:Yan2017:bssrdf-fur)) have recently demonstrated the effectiveness of models based on diffusion in addressing this problem.

While the rendering problem as discussed so far has been challenging enough, Jarabo et al. ([2014a](#cite:Jarabo2014a)) showed the extension of the path integral to not include the steady-state assumption—that is, accounting for the noninfinite speed of light. Time ends up being extremely high frequency, which makes rendering challenging; they showed successful application of density estimation to this problem.

### References

[^1]: Arvo, J. 1986. Backward ray tracing. In *Developments in Ray Tracing, SIGGRAPH ’86 Course Notes*, 259–63.

[^2]: Arvo, J. 1993. Transfer equations in global illumination. In *Global Illumination, SIGGRAPH ’93 Course Notes*, Volume 42, 1:1–30.

[^3]: Arvo, J., and D. Kirk. 1990. Particle transport and image synthesis. *Computer Graphics (SIGGRAPH ’90 Proceedings)* *24*  (4), 63–66.

[^4]: Bako, S., M. Meyer, T. DeRose, and P. Sen. 2019. Offline deep importance sampling for Monte Carlo path tracing. *Computer Graphics Forum* *38*  (7), 527–42.

[^5]: Belcour, L., G. Xie, C. Hery, M. Meyer, W. Jarosz, and D. Nowrouzezahrai. 2018. Integrating clipped spherical harmonics expansions. *ACM Transactions on Graphics* *37*  (2), 19:1–12.

[^6]: Benthin, C., I. Wald, and P. Slusallek. 2003. A scalable approach to interactive global illumination. In *Computer Graphics Forum* *22*  (3), 621–30.

[^7]: Billen, N., and P. Dutré. 2016. Line sampling for direct illumination. *Computer Graphics Forum* *35*  (4), 45–55.

[^8]: Bouchard, G., J.-C. Iehl, V. Ostromoukhov, and P. Poulin. 2013. Improving robustness of Monte-Carlo global illumination with directional regularization. In *SIGGRAPH Asia 2013 Technical Briefs*, 22:1–4.

[^9]: Burke, D., A. Ghosh, and W. Heidrich. 2005. Bidirectional importance sampling for direct illumination. In *Rendering Techniques 2005: 16th Eurographics Workshop on Rendering*, 147–56.

[^10]: Christensen, P. H. 2003. Adjoints and importance in rendering: An overview. *IEEE Transactions on Visualization and Computer Graphics* *9*  (3), 329–40.

[^11]: Clarberg, P., W. Jarosz, T. Akenine-Möller, and H. W. Jensen. 2005. Wavelet importance sampling: Efficiently evaluating products of complex functions. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2005)* *24*  (3), 1166–75.

[^12]: Cline, D., D. Adams, and P. Egbert. 2008. Table-driven adaptive importance sampling. *Computer Graphics Forum (Proceedings of the 2008 Eurographics Symposium on Rendering)* *27*  (4), 1115–23.

[^13]: Cline, D., P. Egbert, J. Talbot, and D. Cardon. 2006. Two stage importance sampling for direct lighting. *Rendering Techniques 2006: 17th Eurographics Workshop on Rendering*, 103–14.

[^14]: Conty Estevez, A., and C. Kulla. 2020. Adaptive caustics rendering in production with photon guiding. *EGSR Industry Track*

[^15]: Coveyou, R. R., V. R. Cain, and K. J. Yost. 1967. Adjoint and importance in Monte Carlo application. *Nuclear Science and Engineering* *27*  (2), 219–34.

[^16]: Crespo, M., A. Jarabo, and A. Muñoz. 2021. Primary-space adaptive control variates using piecewise-polynomial approximations. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (3), 25:1–15.

[^17]: Dachsbacher, C., J. Křivánek, M. Hašan, A. Arbree, B. Walter, and J. Novák. 2014. Scalable realistic rendering with many-light methods. *Computer Graphics Forum* *33*  (1), 88–104.

[^18]: Dahm, K., and A. Keller. 2017. Learning light transport the reinforced way. arXiv:1701.07403 \[cs.LG\].

[^19]: Diolatzis, S., A. Gruson, W. Jakob, D. Nowrouzezahrai, and G. Drettakis. 2020. Practical product path guiding using linearly transformed cosines. *Computer Graphics Forum* *39*  (4), 23–33.

[^20]: Dutré, P., E. P. Lafortune, and Y. D. Willems. 1993. Monte Carlo light tracing with direct computation of pixel intensities. *3rd International Conference on Computational Graphics and Visualisation Techniques*, 128–37.

[^21]: Georgiev, I., J. Křivánek, T. Davidovič, and P. Slusallek. 2012. Light transport simulation with vertex connection and merging. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2012)* *31*  (6), 192:1–10.

[^22]: Ghosh, A., and W. Heidrich. 2006. Correlated visibility sampling for direct illumination. *The Visual Computer* *22*  (9–10), 693–701.

[^23]: Hachisuka, T., A. S. Kaplanyan, and C. Dachsbacher. 2014. Multiplexed Metropolis light transport. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2014)* *33*  (4), 100:1–10.

[^24]: Hachisuka, T., and H. W. Jensen. 2009. Stochastic progressive photon mapping. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2009)* *28*  (5), 141:1–8.

[^25]: Hachisuka, T., J. Pantaleoni, and H. W. Jensen. 2012. A path space extension for robust light transport simulation. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2012)* *31*  (6), 191:1–10.

[^26]: Hachisuka, T., S. Ogaki, and H. W. Jensen. 2008. Progressive photon mapping. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2008)* *27*  (5), 130:1–8.

[^27]: Hall, R. A., and D. P. Greenberg. 1983. A testbed for realistic image synthesis. *IEEE Computer Graphics and Applications* *3*  (8), 10–20.

[^28]: Hart, D., M. Pharr, T. Müller, W. Lopes, M. McGuire, and P. Shirley. 2020. Practical product sampling by fitting and composing warps. *Computer Graphics Forum* *39*  (4), 149–58.

[^29]: Heitz, E., S. Hill, and M. McGuire. 2018. Combining analytic direct illumination and stochastic shadows. *Proceedings of the ACM SIGGRAPH Symposium on Interactive 3D Graphics and Games*, 2:1–11.

[^30]: Herholz, S., O. Elek, J. Vorba, H. Lensch, and J. Křivánek. 2016. Product importance sampling for light transport path guiding. *Computer Graphics Forum* *35*  (4), 67–77.

[^31]: Herzog, R., V. Havran, S. Kinuwaki, K. Myszkowski, and H.-P. Seidel. 2007. Global illumination using photon ray splatting. *Computer Graphics Forum (Proceedings of Eurographics 2007)* *26*  (3), 503–13.

[^32]: Hua, B.-S., A. Gruson, V. Petitjean, M. Zwicker, D. Nowrouzezahrai, E. Eisemann, and T. Hachisuka. 2019. A survey on gradient-domain rendering. *Computer Graphics Forum (Eurographics State of the Art Report)* *38*  (2), 455–72.

[^33]: Huo, Y., R. Wang, R. Zheng, H. Xu, H. Bao, and S.-E. Yoon. 2020. Adaptive incident radiance field sampling and reconstruction using deep reinforcement learning. *ACM Transactions on Graphics* *39*  (1), 6:1–17.

[^34]: Immel, D. S., M. F. Cohen, and D. P. Greenberg. 1986. A radiosity method for non-diffuse environments. In *Computer Graphics (SIGGRAPH ’86 Proceedings)*, Volume 20, 133–42.

[^35]: Jakob, W., and S. Marschner. 2012. Manifold exploration: A Markov chain Monte Carlo technique for rendering scenes with difficult specular transport. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2012)* *31*  (4), 58:1–13.

[^36]: Jendersie, J., and T. Grosch. 2019. Microfacet model regularization for robust light transport. *Computer Graphics Forum* *38*  (4), 39–47.

[^37]: Jensen, H. W. 1995. Importance driven path tracing using the photon map. In *Eurographics Rendering Workshop 1995*, 326–35.

[^38]: Jensen, H. W. 1996. Global illumination using photon maps. In *Eurographics Rendering Workshop 1996*, 21–30.

[^39]: Jensen, H. W. 1997. Rendering caustics on non-Lambertian surfaces. *Computer Graphics Forum* *16*  (1), 57–64.

[^40]: Jensen, H. W. 2001. *Realistic Image Synthesis Using Photon Mapping*. Natick, Massachusetts: A. K. Peters.

[^41]: Křivánek, J., P. Gautron, S. Pattanaik, and K. Bouatouch. 2005. Radiance caching for efficient global illumination computation. *IEEE Transactions on Visualization and Computer Graphics* *11*  (5), 550–61.

[^42]: Kajiya, J. T. 1986. The rendering equation. In *Computer Graphics (SIGGRAPH ’86 Proceedings)* *20*, 143–50.

[^43]: Kaplanyan, A. S., and C. Dachsbacher. 2013. Path space regularization for holistic and robust light transport. *Computer Graphics Forum (Proceedings of Eurographics 2013)* *32*  (2), 63–72.

[^44]: Kaplanyan, A. S., J. Hanika, and C. Dachsbacher. 2014. The natural-constraint representation of the path space for efficient light transport simulation. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2014)* *33*  (4), 102:1–13.

[^45]: Kelemen, C., L. Szirmay-Kalos, G. Antal, and F. Csonka. 2002. A simple and robust mutation strategy for the Metropolis light transport algorithm. *Computer Graphics Forum* *21*  (3), 531–40.

[^46]: Kettunen, M., M. Manzi, M. Aittala, J. Lehtinen, F. Durand, and M. Zwicker. 2015. Gradient-domain path tracing. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2015)* *34*  (4), 123:1–13.

[^47]: Kirk, D. B., and J. Arvo. 1991. Unbiased sampling techniques for image synthesis. *Computer Graphics (SIGGRAPH ’91 Proceedings)*, Volume 25, 153–56.

[^48]: Lafortune, E. P., and Y. D. Willems. 1996. Rendering participating media with bidirectional path tracing. In *Eurographics Rendering Workshop 1996*, 91–100.

[^49]: Lafortune, E., and Y. Willems. 1993. Bi-directional path tracing. *Proceedings of Compugraphics*, 145–53.

[^50]: Lafortune, E., and Y. Willems. 1994. The ambient term as a variance reducing technique for Monte Carlo ray tracing. *Rendering Techniques (Proceedings of the Eurographics Workshop on Rendering)*, 163–71.

[^51]: Lafortune, E., and Y. Willems. 1995. A 5D tree to reduce the variance of Monte Carlo ray tracing. In *Eurographics Workshop on Rendering Techniques 1995*, 11–20.

[^52]: Lehtinen, J., T. Karras, S. Laine, M. Aittala, F. Durand, and T. Aila. 2013. Gradient-domain Metropolis light transport. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2013)* *32*  (4), 95:1–12.

[^53]: Loubet, G., T. Zeltner, N. Holzschuch, and W. Jakob. 2020. Slope-space integrals for specular next event estimation. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *39*  (6), 239:1–13.

[^54]: Müller, T., F. Rousselle, A. Keller, and J. Novák. 2020. Neural control variates. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *39*  (6), 243:1–19.

[^55]: Müller, T., F. Rousselle, J. Novák, and A. Keller. 2021. Real-time neural radiance caching for path tracing. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 36:1–16.

[^56]: Manzi, M., F. Rousselle, M. Kettunen, J. Lehtinen, and M. Zwicker. 2014. Improved sampling for gradient-domain Metropolis light transport. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2014)* *33*  (6), 178:1–12.

[^57]: Manzi, M., M. Kettunen, M. Aittala, J. Lehtinen, F. Durand, and M. Zwicker. 2015. Gradient-domain bidirectional path tracing. *Eurographics Symposium on Rendering—Experimental Ideas & Implementations*

[^58]: Moon, J., and S. Marschner. 2006. Simulating multiple scattering in hair using a photon mapping approach. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2006)* *25*  (3), 1067–74.

[^59]: Moon, J., B. Walter, and S. Marschner. 2008. Efficient multiple scattering in hair using spherical harmonics. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2008)* *27*  (3), 31:1–7.

[^60]: Otsu, H., A. Kaplanyan, J. Hanika, C. Dachsbacher, and T. Hachisuka. 2017. Fusing state spaces for Markov chain Monte Carlo rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 74:1–10.

[^61]: Pantaleoni, J. 2017. Charted Metropolis light transport. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 75:1–14.

[^62]: Pattanaik, S. N., and S. P. Mudur. 1995. Adjoint equations and random walks for illumination computation. *ACM Transactions on Graphics* *14*  (1), 77–102.

[^63]: Pauly, M. 1999. Robust Monte Carlo methods for photorealistic rendering of volumetric effects. Master’s thesis, Universität Kaiserslautern.

[^64]: Pauly, M., T. Kollig, and A. Keller. 2000. Metropolis light transport for participating media. In *Rendering Techniques 2000: 11th Eurographics Workshop on Rendering*, 11–22.

[^65]: Petitjean, V., P. Bauszat, and E. Eisemann. 2018. Spectral gradient sampling for path tracing. *Computer Graphics Forum* *37*  (4), 45–53.

[^66]: Rath, A., P. Grittmann, S. Herholz, P. Vévoda, P. Slusallek, and J. Křivánek. 2020. Variance-aware path guiding. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *39*  (4), 151:1–12.

[^67]: Reibold, R., J. Hanika, A. Jung, and C. Dachsbacher. 2018. Selective guided sampling with complete light transport paths. *ACM Transactions on Graphics* *37*  (6), 223:1–14.

[^68]: Ren, P., J. Wang, M. Gong, S. Lin, X. Tong, and B. Guo. 2013. Global illumination with radiance regression functions. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2013)* *32*  (4), 130:1–12.

[^69]: Rousselle, F., P. Clarberg, L. Leblank, V. Ostromoukhov, and P. Poulin. 2008. Efficient product sampling using hierarchical thresholding. *The Visual Computer (Proceedings of CGI 2008)* *24*  (7–9), 465–74.

[^70]: Rousselle, F., W. Jarosz, J. Novák. 2016. Image-space control variates for rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *35*  (6), 169:1–12.

[^71]: Ruppert, L., S. Herholz, and H. P. A. Lensch. 2020. Robust fitting of parallax-aware mixtures for path guiding. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *39*  (4), 147:1–15.

[^72]: Salesin, K., and W. Jarosz. 2019. Combining point and line samples for direct illumination. *Computer Graphics Forum* *38*  (4), 159–69.

[^73]: Schwarzhaupt, J., H. W. Jensen, and W. Jarosz. 2012. Practical Hessian-based error control for irradiance caching. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *31*  (6), 193:1–10.

[^74]: Shirley, P. 1990. Physically based lighting calculations for computer graphics. Ph.D. thesis, Department of Computer Science, University of Illinois, Urbana–Champaign.

[^75]: Subr, K., D. Nowrouzezahrai, W. Jarosz, J. Kautz, and K. Mitchell. 2014. Error analysis of estimators that use combinations of stochastic sampling strategies for direct illumination. *Computer Graphics Forum (Proceedings of the 2014 Eurographics Symposium on Rendering)* *33*  (4), 93–102.

[^76]: Sun, W., X. Sun, N. A. Carr, D. Nowrouzezahrai, and R. Ramamoorthi. 2017. Gradient-domain vertex connection and merging. *Eurographics Symposium on Rendering—Experimental Ideas and Implementations*

[^77]: Szécsi, L., L. Szirmay-Kalos, and C. Kelemen. 2003. Variance reduction for Russian roulette. *Journal of the World Society for Computer Graphics (WSCG)* *11* (1).

[^78]: Tabellion, E., and A. Lamorlette. 2004. An approximate global illumination system for computer generated films. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2004)* *23*  (3), 469–76.

[^79]: Veach, E., and L. Guibas. 1994. Bidirectional estimators for light transport. In *Fifth Eurographics Workshop on Rendering*, 147–62.

[^80]: Veach, E., and L. J. Guibas. 1995. Optimally combining sampling techniques for Monte Carlo rendering. In *Computer Graphics (SIGGRAPH ’95 Proceedings)*, 419–28.

[^81]: Veach, E., and L. J. Guibas. 1997. Metropolis light transport. In *Computer Graphics (SIGGRAPH ’97 Proceedings)*, 65–76.

[^82]: Vorba, J., O. Karlík, M. Šik, T. Ritschel, and J. Křivánek. 2014. On-line learning of parametric mixture models for light transport simulation. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2014)* *33*  (4), 101:1–11.

[^83]: Wald, I., C. Benthin, and P. Slusallek. 2003. Interactive global illumination in complex and highly occluded environments. In *Eurographics Symposium on Rendering: 14th Eurographics Workshop on Rendering*, 74–81.

[^84]: Wald, I., T. Kollig, C. Benthin, A. Keller, and P. Slusallek. 2002. Interactive global illumination using fast ray tracing. In *Rendering Techniques 2002: 13th Eurographics Workshop on Rendering*, 15–24.

[^85]: Walter, B., A. Arbree, K. Bala, and D. Greenberg. 2006. Multidimensional lightcuts. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2006)* *25*  (3), 1081–88.

[^86]: Walter, B., P. Khungurn, and K. Bala. 2012. Bidirectional lightcuts. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2012)* *31*  (4), 59:1–11.

[^87]: Walter, B., S. Fernandez, A. Arbree, K. Bala, M. Donikian, and D. Greenberg. 2005. Lightcuts: A scalable approach to illumination. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2005)* *24*  (3), 1098–107.

[^88]: Wang, R., and O. Åkerlund. 2009. Bidirectional importance sampling for unstructured illumination. *Computer Graphics Forum (Proceedings of Eurographics 2009)* *28*  (2), 269–78.

[^89]: Weier, P., M. Droske, J. Hanika, A. Weidlich, and J. Vorba. 2021. Optimised path space regularisation. *Computer Graphics Forum (Proceedings of EGSR 2021)* *40*  (4), 139–51.

[^90]: Yuksel, C., and C. Yuksel. 2017. Lighting grid hierarchy for self-illuminating explosions. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 110:1–10.

[^91]: Zeltner, T., I. Georgiev, and W. Jakob. 2020. Specular manifold sampling for rendering high-frequency caustics and glints. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *39*  (4), 149:1–15.

[^92]: Zhao, Y., L. Belcour, and D. Nowrouzezahrai. 2019. View-dependent radiance caching. *Proceedings of Graphics Interface 2019 (GI ’19)*, 22:1–9.

[^93]: Zhu, S., Z. Xu, T. Sun, A. Kuznetsov, M. Meyer, H. W. Jensen, H. Su, and R. Ramamoorthi. 2021. Hierarchical neural reconstruction for path guiding using hybrid path and photon samples. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 35:1–16.

[^94]: Zinke, A., C. Yuksel, A. Weber, and J. Keyser. 2008. Dual scattering approximation for fast multiple scattering in hair. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2008)* *27*  (3), 32:1–10.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[光传输方程]]
- [[路径追踪]]
- [[蒙特卡洛积分]]

**同章节**:
- [[13 Light Transport I Surface Reflection]]
- [[13.1 The Light Transport Equation]]
- [[13.2 Path Tracing]]
- [[13.3 A Simple Path Tracer]]
- [[13.4 A Better Path Tracer]]
