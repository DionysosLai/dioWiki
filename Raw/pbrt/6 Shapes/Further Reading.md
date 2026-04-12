---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Shapes/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
*An Introduction to Ray Tracing* has an extensive survey of algorithms for ray–shape intersection ([Glassner 1989a](#cite:Glassner:IntroRayTracing)). Goldstein and Nagel ([^21]) discussed ray–quadric intersections, and Heckbert ([^31]) discussed the mathematics of quadrics for graphics applications in detail, with many citations to literature in mathematics and other fields. Hanrahan ([^27]) described a system that automates the process of deriving a ray intersection routine for surfaces defined by implicit polynomials; his system emits C source code to perform the intersection test and normal computation for a surface described by a given equation. Mitchell ([^46]) showed that interval arithmetic could be applied to develop algorithms for robustly computing intersections with implicit surfaces that cannot be described by polynomials and are thus more difficult to accurately compute intersections for (more recent work in this area was done by Knoll et al. ([^39])).

Other notable early papers related to ray–shape intersection include Kajiya’s ([^36]) work on computing intersections with surfaces of revolution and procedurally generated fractal terrains. Fournier et al.’s ([^17]) paper on rendering procedural stochastic models and Hart et al.’s ([^29]) paper on finding intersections with fractals illustrate the broad range of shape representations that can be used with ray-tracing algorithms.

The ray–triangle intersection test in Section [6.5](https://pbr-book.org/4ed/Shapes/Triangle_Meshes.html#sec:triangle-mesh) was developed by Woop et al. ([^77]). See Möller and Trumbore ([^42]) for another widely used ray–triangle intersection algorithm. A ray–quadrilateral intersection routine was developed by Lagae and Dutré ([^40]). An interesting approach for developing a fast ray–triangle intersection routine was introduced by Kensler and Shirley ([^38]): they implemented a program that performed a search across the space of mathematically equivalent ray–triangle tests, automatically generating software implementations of variations and then benchmarking them. In the end, they found a more efficient ray–triangle routine than had been in use previously.

Kajiya ([^35]) developed the first algorithm for computing intersections with parametric patches. Subsequent work on more efficient techniques for direct ray intersection with patches includes papers by Stürzlinger ([^64]), Martin et al. ([^45]), Roth et al. ([^60]), and Benthin et al. ([^7]), who also included additional references to previous work. Related to this, Ogaki and Tokuyoshi ([^48]) introduced a technique for directly intersecting smooth surfaces generated from triangle meshes with per-vertex normals.

Ramsey et al. ([^54]) described an algorithm for computing intersections with bilinear patches, though double-precision computation was required for robust results. Reshetov ([^56]) derived a more efficient algorithm that operates in single precision; that algorithm is used in pbrt ’s [BilinearPatch](https://pbr-book.org/4ed/Shapes/Bilinear_Patches.html#BilinearPatch) implementation. See Akenine-Möller et al. ([^2]) for explanations of the algorithms used in its implementation that are related to the distance between lines.

Phong and Crow ([^52]) introduced the idea of interpolating per-vertex shading normals to give the appearance of smooth surfaces from polygonal meshes. The use of shading normals may cause rays reflected from a surface to be on the wrong side of the true surface; Reshetov et al. ([^59]) described a normal interpolation technique that avoids this problem.

The layout of triangle meshes in memory can have a measurable impact on performance. In general, if triangles that are close together in 3D space are close together in memory, cache hit rates will be higher, and overall system performance will benefit. See Yoon et al. ([^79]) and Yoon and Lindstrom ([^78]) for algorithms for creating cache-friendly mesh layouts in memory. Relatedly, reducing the storage required for meshes can improve performance, in addition to making it possible to render more complex scenes; see for example Lauterbach et al. ([^41]).

*Subdivision surfaces* are a widely used representation of smooth surfaces; they were invented by Doo and Sabin ([^16]) and Catmull and Clark ([^12]). Warren’s book provides a good introduction to them ([Warren 2002](#cite:Warren02)). Müller et al. ([^43]) described an approach that refines a subdivision surface on demand for the rays to be tested for intersection with it, and Benthin et al. ([^8], [^9]) described a related approach. A more memory-efficient approach was described by Tejima et al. ([^66]), who converted subdivision surfaces to Bézier patches and intersected rays with those. Previous editions of this book included a section in this chapter on the implementation of subdivision surfaces, which may also be of interest.

The curve intersection algorithm in Section [6.7](https://pbr-book.org/4ed/Shapes/Curves.html#sec:curves) is based on the approach developed by Nakamaru and Ohno ([^47]). Earlier methods for computing ray intersections with generalized cylinders are also applicable to rendering curves, though they are much less efficient ([Bronsvoort and Klok 1985](#cite:Bronsvoort85); [de Voogt et al. 2000](#cite:deVoogt00)). Binder and Keller ([^10]) improved the recursive culling of curve intersections using cylinders to bound the curve in place of axis-aligned bounding boxes. Their approach is better suited for GPUs than the current implementation in the [Curve](https://pbr-book.org/4ed/Shapes/Curves.html#Curve) shape, as it uses a compact bit field to record work to be done, in place of recursive evaluation.

More efficient intersection algorithms for curves have recently been developed by Reshetov ([^58]) and Reshetov and Luebke ([^57]). Related is a tube primitive described by a poly-line with a specified radius at each vertex that Han et al. ([^26]) provided an efficient intersection routine for.

One challenge with rendering thin geometry like hair and fur is that thin geometry may require many pixel samples to be accurately resolved, which in turn increases rendering time. One approach to this problem was described by Qin et al. ([^53]), who used cone tracing for rendering fur, where narrow cones are traced instead of rays. In turn, all the curves that intersect a cone can be considered in computing the cone’s contribution, allowing high-quality rendering with a small number of cones per pixel.

An excellent introduction to differential geometry was written by Gray ([^22]); Section 14.3 of his book presents the Weingarten equations.

#### Intersection Accuracy

Higham’s ([^33]) book on floating-point computation is excellent; it also develops the notation that we have used in Section [6.8](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#sec:fp-error). Other good references to this topic are Wilkinson ([^75]) and Goldberg ([^20]). While we have derived floating-point error bounds manually, see the *Gappa* system by Daumas and Melquiond ([^15]) for a tool that automatically derives forward error bounds of floating-point computations. The *Herbgrind* ([Sanchez-Stern et al. 2018](#cite:SanchezStern2018)) system implements an interesting approach, automatically finding floating-point computations that suffer from excessive error during the course of a program’s execution.

The incorrect self-intersection problem has been a known problem for ray-tracing practitioners for quite some time (Haines [^24]; Amanatides and Mitchell [^3]). In addition to offsetting rays by an “epsilon” at their origin, approaches that have been suggested include ignoring intersections with the object that was previously intersected; “root polishing” (Haines [^24]; Woo et al. [^76]), where the computed intersection point is refined to become more numerically accurate; and using higher-precision floating-point representations (e.g., double instead of float).

Kalra and Barr ([^37]) and Dammertz and Keller ([^14]) developed algorithms for numerically robust intersections based on recursively subdividing object bounding boxes, discarding boxes that do not encompass the object’s surface, and discarding boxes missed by the ray. Both of these approaches are much less efficient than traditional ray–object intersection algorithms as well as the techniques introduced in Section [6.8](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#sec:fp-error).

Ize showed how to perform numerically robust ray–bounding box intersections (Ize [^34]); his approach is implemented in Section [6.8.2](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#sec:conservative-ray-bounds). (With a more careful derivation, he showed that a scale factor of can be used to increase tMax, rather than the we derived.) Wächter ([^72]) discussed self-intersection issues in his thesis; he suggested recomputing the intersection point starting from the initial intersection (root polishing) and offsetting spawned rays along the normal by a fixed small fraction of the intersection point’s magnitude. The approach implemented in this chapter uses his approach of offsetting ray origins along the normal but uses conservative bounds on the offsets based on the numerical error present in computed intersection points. (As it turns out, our bounds are generally tighter than Wächter’s offsets while also being provably conservative.)

The method used for computing accurate discriminants for ray–quadratic intersections in Section [6.8.3](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#sec:accurate-quadratic-discriminants) is due to Hearn and Baker ([^30]), via Haines et al. ([^25]).

Geometric accuracy has seen much more attention in computational geometry than in rendering. Examples include Salesin et al. ([^61]), who introduced techniques to derive robust primitive operations for computational geometry that accounted for floating-point round-off error, and Shewchuk ([^62]), who applied adaptive-precision floating-point arithmetic to geometric predicates, using just enough precision to compute a correct result for given input values.

The precision requirements of ray tracing have implications beyond practical implementation, which has been our focus. Reif et al. ([^55]) showed how to construct Turing machines based entirely on ray tracing and the geometric optics, which implies that ray tracing is *undecidable* in the sense of complexity theory. Yet in practice, optical computing systems can be constructed, though they are not able to solve undecidable problems. Blakey ([^11]) showed that this can be explained by careful consideration of such optical Turing machines’ precision requirements, which can grow exponentially.

#### Sampling Shapes

Turk ([^68]) described two approaches for uniformly sampling the surface area of triangles. The approach implemented in [SampleUniformTriangle()](https://pbr-book.org/4ed/Shapes/Triangle_Meshes.html#SampleUniformTriangle), which is more efficient and better preserves sample stratification than the algorithms given by Turk, is due to Talbot ([^65]) and Heitz ([^32]). Shirley et al. ([^63]) derived methods for sampling a number of other shapes, and Arvo and Novins ([^4]) showed how to sample convex quadrilaterals.

The aforementioned approaches are all based on warping samples from the unit square to the surface of the shape; an interesting alternative was given by Basu and Owen ([^5], [^6]), who showed how to recursively decompose triangles and disks to directly generate low-discrepancy points on their surfaces. Marques et al. ([^44]) showed how to generate low-discrepancy samples directly on the unit sphere; see also Christensen’s report ([^13]), which shows an error reduction from imposing structure on the distribution of multiple sample points on disk light sources.

Uniformly sampling the visible area of a shape from a reference point is an improvement to uniform area sampling for direct lighting calculations. Gardner et al. ([^19]) and Zimmerman ([^80]) derived methods to do so for cylinders, and Wang et al. ([^74]) found an algorithm to sample the visible area of cones. (For planar shapes like triangles, the visible area is trivially the entire area.)

Uniform solid angle sampling of shapes has also seen attention by a number of researchers. Wang ([^73]) introduced an approach for solid angle sampling of spheres. Arvo showed how to sample the projection of a triangle on the sphere of directions with respect to a reference point (Arvo [1995b](#cite:Arvo1995b)); his approach is implemented in [SampleSphericalTriangle()](https://pbr-book.org/4ed/Shapes/Triangle_Meshes.html#SampleSphericalTriangle). (A more efficient approach to solid angle sampling of triangles was recently developed by Peters ([2021b](#cite:Peters2021polygonal), Section 5).) Ureña et al. ([^71]) and Pekelis and Hery ([^49]) developed analogous techniques for sampling quadrilateral light sources; Ureña et al.’s method is used in [SampleSphericalRectangle()](https://pbr-book.org/4ed/Shapes/Bilinear_Patches.html#SampleSphericalRectangle). (To better understand these techniques for sampling projected polygons, Donnay’s book on spherical trigonometry provides helpful background ([Donnay 1945](#cite:Donnay1945)).) The approach implemented in Section [6.2.4](https://pbr-book.org/4ed/Shapes/Spheres.html#sec:sphere-sampling) to convert an angle in a cone to a point on a sphere was derived by Akalin ([^1]).

The algorithm for inverting the spherical triangle sampling algorithm that is implemented in [InvertSphericalTriangleSample()](https://pbr-book.org/4ed/Shapes/Triangle_Meshes.html#InvertSphericalTriangleSample) is due to Arvo ([2001b](#cite:Arvo2001:code)).

Gamito ([^18]) presented an approach for uniform solid angle sampling of disk and cylindrical lights based on bounding the solid angle they subtend in order to fit a quadrilateral, which is then sampled using Ureña et al.’s method ([^71]). Samples that do not correspond to points on the light source are rejected. A related approach was developed by Tsai et al. ([^67]), who approximate shapes with collections of triangles that are then sampled by solid angle. Guillén et al. ([^23]) subsequently developed an algorithm for directly sampling disks by solid angle that avoids rejection sampling.

Spheres are the only shapes for which we are aware of algorithms for direct sampling of their projected solid angle. An algorithm to do so was presented by Ureña and Georgiev ([^70]). Peters and Dachsbacher developed a more efficient approach ([^51]) and Peters ([^50]) described how to use this method to compute the PDF associated with a direction so that it can be used with multiple importance sampling.

A variety of additional techniques for projected solid angle sampling have been developed. Arvo ([2001a](#cite:Arvo2001)) described a general framework for deriving sampling algorithms and showed its application to projected solid angle sampling of triangles, though numeric inversion of the associated CDF is required. Ureña ([^69]) approximated projected solid angle sampling of triangles by progressively decomposing them into smaller triangles until solid angle sampling is effectively equivalent. The approach based on warping uniform samples to approximate projected solid angle sampling that we implemented for triangles and quadrilateral bilinear patches was described by Hart et al. ([^28]). Peters ([2021b](#cite:Peters2021polygonal)) has recently shown how to efficiently and accurately perform projected solid angle sampling of polygons.

### References

[^1]: Akalin, F. 2015. Sampling the visible sphere. [https://www.akalin.com/sampling-visible-sphere](https://www.akalin.com/sampling-visible-sphere)

[^2]: Akenine-Möller, T., E. Haines, N. Hoffman, A. Peesce, M. Iwanicki, and S. Hillaire. 2018. *Real-Time Rendering* (4th ed.). Boca Raton, FL: CRC Press.

[^3]: Amanatides, J., and D. P. Mitchell. 1990. Some regularization problems in ray tracing. In *Proceedings of Graphics Interface 1990*, 221–28.

[^4]: Arvo, J., and K. Novins. 2007. Stratified sampling of convex quadrilaterals. *Journal of Graphics, GPU, and Game Tools* *12*  (2), 1–12.

[^5]: Basu, K., and A. B. Owen. 2015. Low discrepancy constructions in the triangle. *SIAM Journal on Numerical Analysis* *53*  (2), 743–61.

[^6]: Basu, K., and A. B. Owen. 2017. Scrambled geometric net integration over general product spaces. *Foundations of Computational Mathematics* *17*, 467–96.

[^7]: Benthin, C., I. Wald, and P. Slusallek. 2006. Techniques for interactive ray tracing of Bézier surfaces. *Journal of Graphics, GPU, and Game Tools* *11*  (2), 1–16.

[^8]: Benthin, C., S. Boulos, D. Lacewell, and I. Wald. 2007. Packet-based ray tracing of Catmull–Clark subdivision surfaces. *SCI Institute Technical Report, No. UUSCI-2007-011*. University of Utah.

[^9]: Benthin, C., S. Woop, M. Nießner, K. Selgrad, and I. Wald. 2015. Efficient ray tracing of subdivision surfaces using tessellation caching. *Proceedings of the 7th Conference on High Performance Graphics (HPG ’15)*, 5–12.

[^10]: Binder, N., and A. Keller. 2018. Fast, high precision ray/fiber intersection using tight, disjoint bounding volumes. arXiv:1811.03374 \[cs.GR\].

[^11]: Blakey, E. 2012. Ray tracing—computing the incomputable? *Developments in Computational Models*, 32–40.

[^12]: Catmull, E., and J. Clark. 1978. Recursively generated B-spline surfaces on arbitrary topological meshes. *Computer-Aided Design* *10*, 350–55.

[^13]: Christensen, P. 2018. Progressive sampling strategies for disk light sources. *Pixar Animation Studios Technical Memo 18-02*

[^14]: Dammertz, H., and A. Keller. 2006. Improving ray tracing precision by object space intersection computation. *IEEE Symposium on Interactive Ray Tracing*, 25–31.

[^15]: Daumas, M., and G. Melquiond. 2010. Certification of bounds on expressions involving rounded operators. *ACM Transactions on Mathematical Software* *37*  (1), 2:1–20.

[^16]: Doo, D., and M. Sabin. 1978. Behaviour of recursive division surfaces near extraordinary points. *Computer-Aided Design* *10*  (6), 356–60.

[^17]: Fournier, A., D. Fussel, and L. Carpenter. 1982. Computer rendering of stochastic models. *Communications of the ACM* *25*  (6), 371–84.

[^18]: Gamito, M. N. 2016. Solid angle sampling of disk and cylinder lights. *Computer Graphics Forum* *35*  (4), 25–36.

[^19]: Gardner, R. P., H. K. Choi, M. Mickael, A. M. Yacout, Y. Yin, and K. Verghese. 1987. Algorithms for forcing scattered radiation to spherical, planar circular, and right circular cylindrical detectors for Monte Carlo simulation. *Nuclear Science and Engineering* *95*, 245–56.

[^20]: Goldberg, D. 1991. What every computer scientist should know about floating-point arithmetic. *ACM Computing Surveys* *23*  (1), 5–48.

[^21]: Goldstein, R. A., and R. Nagel. 1971. 3-D visual simulation. *Simulation* *16*  (1), 25–31.

[^22]: Gray, A. 1993. *Modern Differential Geometry of Curves and Surfaces*. Boca Raton, Florida: CRC Press.

[^23]: Guillén, I., C. Ureña, A. King, M. Fajardo, I. Georgiev, J. López-Moreno, and A. Jarabo. 2017. Area-preserving parameterizations for spherical ellipses. *Computer Graphics Forum* *36*  (4), 179–87.

[^24]: Haines, E. A. 1989. Essential ray tracing algorithms. In A. Glassner (ed.), *An Introduction to Ray Tracing*, 33–78. San Diego: Academic Press.

[^25]: Haines, E., J. Günther, and T. Akenine-Möller. 2019. Precision improvements for ray/sphere intersection. In E. Haines and T. Akenine-Möller (eds.), *Ray Tracing Gems*, 7–14. Berkeley: Apress.

[^26]: Han, M., I. Wald, W. Usher, Q. Wu, F. Wang, V. Pascucci, C. D. Hansen, and C. R. Johnson. 2019. Ray tracing generalized tube primitives: Method and applications. *Computer Graphics Forum* *38*  (3), 467–78.

[^27]: Hanrahan, P. 1983. Ray tracing algebraic surfaces. *Computer Graphics (Proceedings of SIGGRAPH ’83)* *17*, 83–90.

[^28]: Hart, D., M. Pharr, T. Müller, W. Lopes, M. McGuire, and P. Shirley. 2020. Practical product sampling by fitting and composing warps. *Computer Graphics Forum* *39*  (4), 149–58.

[^29]: Hart, J. C., D. J. Sandin, and L. H. Kauffman. 1989. Ray tracing deterministic 3-D fractals. *Computer Graphics (Proceedings of SIGGRAPH ’89)* *23*, 289–96.

[^30]: Hearn, D. D., and M. P. Baker. 2004. *Computer Graphics with OpenGL* (3rd ed.). Boston: Pearson.

[^31]: Heckbert, P. S. 1984. *The Mathematics of Quadric Surface Rendering and SOID.* 3-D Technical Memo, New York Institute of Technology Computer Graphics Lab.

[^32]: Heitz, E. 2019. A low-distortion map between triangle and square. *Technical Report*

[^33]: Higham, N. J. 2002. *Accuracy and Stability of Numerical Algorithms* (2nd ed.). Philadelphia: Society for Industrial and Applied Mathematics.

[^34]: Ize, T. 2013. Robust BVH ray traversal. *Journal of Computer Graphics Techniques (JCGT)* *2*  (2), 12–27.

[^35]: Kajiya, J. T. 1982. Ray tracing parametric patches. In *Computer Graphics (SIGGRAPH 1982 Conference Proceedings)*, 245–54.

[^36]: Kajiya, J. T. 1983. New techniques for ray tracing procedurally defined objects. In *Computer Graphics (Proceedings of SIGGRAPH ’83)* *17*, 91–102.

[^37]: Kalra, D., and A. H. Barr. 1989. Guaranteed ray intersections with implicit surfaces. In *Computer Graphics (Proceedings of SIGGRAPH ’89)*, Volume 23, 297–306.

[^38]: Kensler, A., and P. Shirley. 2006. Optimizing ray-triangle intersection via automated search. In *IEEE Symposium on Interactive Ray Tracing*, 33–38.

[^39]: Knoll, A., Y. Hijazi, C. D. Hansen, I. Wald, and H. Hagen. 2009. Fast ray tracing of arbitrary implicit surfaces with interval and affine arithmetic. *Computer Graphics Forum* *28*  (1), 26–40.

[^40]: Lagae, A., and P. Dutré. 2005. An efficient ray-quadrilateral intersection test. *Journal of Graphics Tools* *10*  (4), 23–32.

[^41]: Lauterbach, C., S.-E. Yoon, M. Tang, and D. Manocha. 2008. ReduceM: Interactive and memory efficient ray tracing of large models. *Computer Graphics Forum* *27*  (4), 1313–21.

[^42]: Möller, T., and B. Trumbore. 1997. Fast, minimum storage ray–triangle intersection. *Journal of Graphics Tools* *2*  (1), 21–28.

[^43]: Müller, K., T. Techmann, and D. Fellner. 2003. Adaptive ray tracing of subdivision surfaces. *Computer Graphics Forum* *22*  (3), 553–62.

[^44]: Marques, R., C. Bouville, M. Ribardière, L. P. Santos, and K. Bouatouch. 2013. Spherical Fibonacci point sets for illumination integrals. *Computer Graphics Forum (Proceedings of the 2013 Eurographics Symposium on Rendering)* *32*  (4), 134–43.

[^45]: Martin, W., E. Cohen, R. Fish, and P. S. Shirley. 2000. Practical ray tracing of trimmed NURBS surfaces. *Journal of Graphics Tools* *5*  (1), 27–52.

[^46]: Mitchell, D. P. 1990. Robust ray intersection with interval arithmetic. In *Proceedings of Graphics Interface 1990*, 68–74.

[^47]: Nakamaru, K., and Y. Ohno. 2002. Ray tracing for curves primitive. In *Journal of WSCG (WSCG 2002 Proceedings)* *10*, 311–16.

[^48]: Ogaki, S., and Y. Tokuyoshi. 2011. Direct ray tracing of Phong tessellation. *Computer Graphics Forum (Proceedings of the 2011 Eurographics Symposium on Rendering)* *30*  (4), 1337–44.

[^49]: Pekelis, L., and C. Hery. 2014. A statistical framework for comparing importance sampling methods, and an application to rectangular lights. *Pixar Technical Memo 14-01*

[^50]: Peters, C. 2019. Sampling projected spherical caps with multiple importance sampling. [http://momentsingraphics.de/SphericalCapMIS.html](http://momentsingraphics.de/SphericalCapMIS.html)

[^51]: Peters, C., and C. Dachsbacher. 2019. Sampling projected spherical caps in real time. *Proceedings of the ACM on Computer Graphics and Interactive Techniques* *2*  (1), 1:1–16.

[^52]: Phong, B.-T., and F. C. Crow. 1975. Improved rendition of polygonal models of curved surfaces. In *Proceedings of the 2nd USA–Japan Computer Conference*

[^53]: Qin, H., M. Chai, Q. Hou, Z. Ren, and K. Zhou. 2014. Cone tracing for furry object rendering. *IEEE Transactions on Visualization and Computer Graphics* *20*  (8), 1178–88.

[^54]: Ramsey, S. D., K. Potter, and C. Hansen. 2004. Ray bilinear patch intersections. *Journal of Graphics Tools* *9*  (3), 41–47.

[^55]: Reif, J. H., J. D. Tygar, and A. Yoshida. 1994. Computability and complexity of ray tracing. *Discrete and Computational Geometry* *11*, 265–88.

[^56]: Reshetov, A. 2019. Cool patches: A geometric approach to ray/bilinear patch intersections. In E. Haines and T. Akenine-Möller (eds.), *Ray Tracing Gems*, 95–109. Berkeley: Apress.

[^57]: Reshetov, A., and D. Luebke. 2018. Phantom ray-hair intersector. *Proceedings of the ACM on Computer Graphics and Interactive Techniques* *1*  (2), 34:1–22.

[^58]: Reshetov, R. 2017. Exploiting Budan–Fourier and Vincent’s theorems for ray tracing 3D Bézier curves. *Proceedings of High Performance Graphics (HPG ’17)*, 5:1–11.

[^59]: Reshetov, R., A. Soupikov, and W. R. Mark. 2010. Consistent normal interpolation. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *29*  (6), 142:1–8.

[^60]: Roth, S. H., P. Diezi, and M. Gross. 2001. Ray tracing triangular Bézier patches. In *Computer Graphics Forum (Eurographics 2001 Conference Proceedings)* *20*  (3), 422–30.

[^61]: Salesin, D., J. Stolfi, and L. Guibas. 1989. Epsilon geometry: Building robust algorithms from imprecise computations. In *Proceedings of the Fifth Annual Symposium on Computational Geometry (SCG ’89)*, 208–17.

[^62]: Shewchuk, J. R. 1997. Adaptive precision floating-point arithmetic and fast robust geometric predicates. *Discrete & Computational Geometry* *18*, 305–63.

[^63]: Shirley, P., C. Y. Wang, and K. Zimmerman. 1996. Monte Carlo techniques for direct lighting calculations. *ACM Transactions on Graphics* *15*  (1), 1–36.

[^64]: Stürzlinger, W. 1998. Ray tracing triangular trimmed free-form surfaces. *IEEE Transactions on Visualization and Computer Graphics* *4*  (3), 202–14.

[^65]: Talbot, J. 2011. Personal communication.

[^66]: Tejima, T., M. Fujita, and T. Matsuoka. 2015. Direct ray tracing of full-featured subdivision surfaces with Bézier clipping. *Journal of Computer Graphics Techniques (JCGT)* *4*  (1), 69–83.

[^67]: Tsai, Y. Y., C. M. Wang, C. H. Chang, and Y. M. Cheng. 2006. Tunable bounding volumes for Monte Carlo applications. *Lecture Notes in Computer Science* *3980*, 171–80.

[^68]: Turk, G. 1990. Generating random points in triangles. In A. S. Glassner (ed.), *Graphics Gems I*, 24–28. San Diego: Academic Press.

[^69]: Ureña, C. 2000. Computation of irradiance from triangles by adaptive sampling. *Computer Graphics Forum 19*  (2), 165–71.

[^70]: Ureña, C., and I. Georgiev. 2018. Stratified sampling of projected spherical caps. *Computer Graphics Forum* *37*  (4), 13–20.

[^71]: Ureña, C., M. Fajardo, and A. King. 2013. An area-preserving parametrization for spherical rectangles. *Computer Graphics Forum (Proceedings of the 2013 Eurographics Symposium on Rendering)* *32*  (4), 59–66.

[^72]: Wächter, C. A. 2008. Quasi Monte Carlo light transport simulation by efficient ray tracing. Ph.D. thesis, University of Ulm.

[^73]: Wang, C. 1992. Physically correct direct lighting for distribution ray tracing. In D. Kirk (ed.), *Graphics Gems III*, 271–74. San Diego: Academic Press.

[^74]: Wang, C.-M., C.-H. Chang, N.-C. Hwang, and Y.-Y. Tsai. 2006. A novel algorithm for sampling uniformly in the directional space of a cone. *IEICE Transactions on Fundamentals of Electronics, Communications and Computer Sciences* *89*  (9), 2351–55.

[^75]: Wilkinson, J. H. 1994. *Rounding Errors in Algebraic Processes*. New York: Dover Publications, Inc. Originally published by Prentice-Hall Inc., 1963.

[^76]: Woo, A., A. Pearce, and M. Ouellette. 1996. It’s really not a rendering bug, you see…. *IEEE Computer Graphics and Applications* *16*  (5), 21–25.

[^77]: Woop, S., C. Benthin, and I. Wald. 2013. Watertight ray/triangle intersection. *Journal of Computer Graphics Techniques (JCGT)* *2*  (1), 65–82.

[^78]: Yoon, S.-E., and P. Lindstrom. 2006. Mesh layouts for block-based caches. IEEE Transactions on Visualization and Computer Graphics *12*  (5), 1213–20.

[^79]: Yoon, S.-E., P. Lindstrom, V. Pascucci, and D. Manocha. 2005. Cache-oblivious mesh layouts. In *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2005)* *24*  (3), 886–93.

[^80]: Zimmerman, K. 1995. Direct lighting models for ray tracing with cylindrical lamps. In *Graphics Gems V*, 285–89. San Diego: Academic Press.