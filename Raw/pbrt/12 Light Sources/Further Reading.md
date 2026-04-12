---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Light_Sources/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
#### Light Emission Descriptions

Warn ([^45]) developed early models of light sources with nonisotropic emission distributions, including the spotlight model used in this chapter. Verbeck and Greenberg ([^42]) also described a number of techniques for modeling light sources that are now classic parts of the light modeling toolbox. Barzel ([^3]) described a highly parameterized model for light sources, including multiple parameters for controlling rate of falloff, the area of space that is illuminated, and so on. Bjorke ([^9]) described a number of additional techniques for shaping illumination for artistic effect. (Many parts of the Barzel and Bjorke approaches are not physically based, however.)

The goniophotometric light source approximation is widely used to model area light sources in the field of illumination engineering. The rule of thumb there is that once a reference point is five times an area light source’s radius away from it, a point light approximation has sufficient accuracy for most applications. File format standards have been developed for encoding goniophotometric diagrams for these applications (Illuminating Engineering Society of North America [^23]). Many lighting fixture manufacturers provide data in these formats on their websites.

Ashdown ([^1]) proposed a more sophisticated light source model than goniophotometric; he measured the directional distribution of emitted radiance at a large number of points around a light source and described how to use the resulting 4D table to compute the received radiance distribution at other points. Another generalization of goniometric lights was suggested by Heidrich et al. ([^22]), who represented light sources as a 4D exitant *lightfield* —essentially a function of both position and direction—and showed how to use this representation for rendering. Additional work in this area was done by Goesele et al. ([^17]) and Mas et al. ([^30]), who introduced a more space-efficient representation and improved rendering efficiency.

Peters ([2021a](#cite:Peters2021linear)) has developed efficient techniques for sampling lights defined by lines (i.e., infinitesimally thin cylinders) and shown how to sample the product of lighting and the BRDF using linearly transformed cosines ([Heitz et al. 2016a](#cite:Heitz2016)).

Real-world light sources are often fairly complex, including carefully designed systems of mirrors and lenses to shape the distribution of light emitted by the light source. (Consider, for example, the headlights on a car, where it is important to evenly illuminate the surface of the road without shining too much light in the eyes of approaching drivers.) All the corresponding specular reflection and transmission is challenging for light transport algorithms. It can therefore be worthwhile to do some precomputation to create a representation of light sources’ final emission distributions after all of this scattering that is then used as the light source model for rendering. To this end, Kniep et al. ([^26]) proposed tracing the paths of photons leaving the light’s filament until they hit a bounding surface around the light. They then recorded the position and direction of outgoing photons and used this information when computing illumination at points in the scene. Velázquez-Armendáriz et al. ([^41]) showed how to compute a set of point lights with directionally varying emission distributions to model emitted radiance from complex light sources. They then approximated the radiance distribution in the light interior using spherical harmonics. More recently, Zhu et al. ([^47]) applied a neural representation to complex lights, encoding lights’ radiance distributions and view-dependent sampling distributions and opacities in neural networks.

#### Illumination from Environment Maps

Blinn and Newell ([^10]) first introduced the idea of environment maps and their use for simulating illumination, although they only considered illumination of specular objects. Greene ([^18]) further refined these ideas, considering antialiasing and different representations for environment maps. Nishita and Nakamae ([^32]) developed algorithms for efficiently rendering objects illuminated by hemispherical skylights and generated some of the first images that showed off that distinctive lighting effect. Miller and Hoffman ([^31]) were the first to consider using arbitrary environment maps to illuminate objects with diffuse and glossy BRDFs. Debevec ([^14]) later extended this work and investigated issues related to capturing images of real environments.

Representing illumination from the sun and sky is a particularly important application of infinite light sources; the “Further Reading” section in Chapter [14](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering.html#chap:volume-integration) includes a number of references related to simulating skylight scattering. Directly measuring illumination from the sky is also an effective way to find accurate skylight illumination; see Kider et al. ([^25]) for details of a system built to do this.

pbrt ’s infinite area light source models incident radiance from the light as purely a function of direction. Especially for indoor scenes, this assumption can be fairly inaccurate; position matters as well. Unger et al. ([^38]) captured the incident radiance as a function of direction at many different locations in a real-world scene and used this representation for rendering. Unger et al. ([^39]) improved on this work and showed how to decimate the samples to reduce storage requirements without introducing too much error. Lu et al. ([^28]) developed techniques for efficiently importance sampling these light sources.

The use of the allowIncompletePDF parameter to avoid generating low-probability samples from infinite light sources in the presence of multiple importance sampling is an application of MIS compensation, which was developed by Karlík et al. ([^24]).

Subr and Arvo ([2007b](#cite:Subr07b)) developed an efficient technique for sampling environment map light sources that not only accounts for the term from the scattering equation but also only generates samples in the hemisphere around the surface normal. More recently, Conty Estevez and Lecocq ([^13]) introduced a technique for sampling according to the product of the BSDF and the environment map based on discretizing the environment map into coarse grids of pixels, conservatively evaluating the maximum of the BSDF over the corresponding sets of directions, and then choosing a region of the environment map according to the product of BSDF and pixel values. Given a selected grid cell, conventional environment map sampling is applied. (See also the “Further Reading” section in Chapter [13](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection.html#chap:light-transport) for further references to light and BSDF product sampling algorithms.)

When environment maps are used for illuminating indoor scenes, many incident directions may be blocked by the building structure. Bitterli et al. ([^8]) developed the environment map rectification approach to this problem that we have implemented in the [PortalImageInfiniteLight](https://pbr-book.org/4ed/Light_Sources/Infinite_Area_Lights.html#PortalImageInfiniteLight). One shortcoming of Bitterli et al.’s approach is that the image must be rectified for each plane in which there is a portal. Ogaki ([^33]) addresses this issue by building a BVH over the portals using Conty Estevez and Kulla’s light BVH ([^12]) and then decomposing portals into triangles to sample a specific direction according to the environment map.

Sampling-based approaches can also be used to account for environment map visibility. Bashford-Rogers et al. ([^4]) developed a two-pass algorithm where a first pass from the camera finds directions that reach the environment map; this information is used to create sampling distributions for use in a second rendering pass. Atanasov et al. ([^2]) also applied a two-pass algorithm to the task, furthermore discretizing regions of the scene in order to account for different parts of the environment map being visible in different regions of the scene.

#### Optimizing Visibility Testing

As discussed in Chapter [6](https://pbr-book.org/4ed/Shapes.html#chap:shapes), one way to reduce the time spent tracing shadow rays is to have methods like [Shape::IntersectP()](https://pbr-book.org/4ed/Shapes/Basic_Shape_Interface.html#Shape::IntersectP) and [Primitive::IntersectP()](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Primitive_Interface_and_Geometric_Primitives.html#Primitive::IntersectP) that just check for any occlusion along a ray without bothering to compute the geometric information at the intersection point.

Another approach for optimizing ray tracing for shadow rays is the *shadow cache*, where each light stores a pointer to the last primitive that occluded a shadow ray to the light. That primitive is checked first to see if it occludes subsequent shadow rays before the ray is passed to the acceleration structure ([^20]). Pearce ([^34]) pointed out that the shadow cache does not work well if the scene has finely tessellated geometry; it may be better to cache the BVH node that held the last occluder, for instance. (The shadow cache can similarly be defeated when multiple levels of reflection and refraction are present or when Monte Carlo ray-tracing techniques are used.) Hart et al. ([^21]) developed a generalization of the shadow cache that tracks which objects block light from particular light sources and clips their geometry against the light-source geometry so that shadow rays do not need to be traced toward the parts of the light that are certain to be occluded.

A related technique, described by Haines and Greenberg ([^20]), is the *light buffer* for point light sources, where the light discretizes the directions around it and determines which objects are visible along each set of directions (and are thus potential occluding objects for shadow rays). A related optimization is *shaft culling*, which takes advantage of coherence among groups of rays traced in a similar set of directions (e.g., shadow rays from a single point to points on an area light source). With shaft culling, a shaft that bounds a collection of rays is computed and then the objects in the scene that penetrate the shaft are found. For all the rays in the shaft, it is only necessary to check for intersections with those objects that intersect the shaft, and the expense of ray intersection acceleration structure traversal for each of the rays is avoided ([Haines and Wallace 1994](#cite:Haines94)).

Woo and Amanatides ([^46]) classified which lights are visible, not visible, and partially visible in different parts of the scene and stored this information in a voxel-based 3D data structure, using the information to save shadow ray tests. Fernandez, Bala, and Greenberg ([^16]) developed a similar approach based on spatial decomposition that stores references to important blockers in each voxel and also builds up this information on demand during rendering. A related approach to reducing the cost of shadow rays is visibility caching, where the point-to-point visibility function’s value is cached for clusters of points on surfaces in the scene (Clarberg and Akenine-Möller [2008b](#cite:Clarberg2008b); Popov et al. [^35]).

For complex models, simplified versions of their geometry can be used for shadow ray intersections. For example, the simplification envelopes described by Cohen et al. ([^11]) can create a simplified mesh that bounds a given mesh from both the inside and the outside. If a ray misses the mesh that bounds a complex model from the outside or intersects the mesh that bounds it from the inside, then no further shadow processing is necessary. Only the uncertain remaining cases need to be intersected against the full geometry. A related technique is described by Lukaszewski ([^29]), who uses the Minkowski sum to effectively expand primitives (or bounds of primitives) in the scene so that intersecting one ray against one of these primitives can determine if any of a collection of rays might have intersected the actual primitives.

The expense of tracing shadow rays to light sources can be significant; a number of techniques have been developed to improve the efficiency of this part of the rendering computation. Billen et al. ([^6]) tested only a random subset of potential occluders for intersections; a compensation term ensured that the result was unbiased. Following work showed how to use simplified geometry for some shadow tests while still computing the correct result overall (Billen et al. [^5]).

#### Many-Light Sampling

A number of approaches have been developed to efficiently render scenes with hundreds or thousands of light sources. Early work on this problem was done by Ward ([^44]) and Shirley et al. ([^36]).

Wald et al. ([^43]) suggested rendering an image with path tracing and a very low sampling rate (e.g., one path per pixel), recording information about which of the light sources made some contribution to the image. This information is then used to set probabilities for sampling each light. Donikian et al. ([^15]) adaptively found PDFs for sampling lights through an iterative process of taking a number of light samples, noting which ones were effective, and reusing this information at nearby pixels. The “lightcuts” algorithm, described in the “Further Reading” section of Chapter [13](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection.html#chap:light-transport), also addresses this problem.

Tokuyoshi and Harada ([^37]) organized lights in trees of bounding spheres and stochastically culled them when shading. Conty Estevez and Kulla ([^12]) organized lights in BVHs and introduced effective approaches for building light BVHs and sampling lights stored in them. pbrt ’s [BVHLightSampler](https://pbr-book.org/4ed/Light_Sources/Light_Sampling.html#BVHLightSampler) is directly based on their approach. (The *Iray* renderer uses a BVH in a similar fashion for light sampling ([Keller et al. 2017](#cite:Keller2017)).) Conty Estevez and Kulla’s approach was subsequently improved by Liu et al. ([2019b](#cite:Liu2019)), who incorporated the BSDF in the sampling weight computations.

Incorporating light visibility into the sampling process can substantially improve the results. Vévoda et al. ([^40]) clustered lights and tracked visibility to them, applying Bayesian regression to learn how to effectively sample lights. Guo et al. ([^19]) cached information about voxel-to-voxel visibility in a discretization of the scene, which can either be used for Russian roulette or for light importance sampling. Bitterli et al. ([^7]) showed how to apply spatial and temporal resampling of light samples that include visibility in order to achieve high-quality results with few shadow rays per pixel.

The “bit trail” technique used to encode the path from the root to each light at the leaves of pbrt ’s [BVHLightSampler](https://pbr-book.org/4ed/Light_Sources/Light_Sampling.html#BVHLightSampler) is due to Laine ([^27]).

### References

[^1]: Ashdown, I. 1993. Near-field photometry: A new approach. *Journal of the Illuminating Engineering Society* *22*  (1), 163–80.

[^2]: Atanasov, A., V. Koylazov, B. Taskov, A. Soklev, V. Chizhov, and J. Křivánek. 2018. Adaptive environment sampling on CPU and GPU. In *ACM SIGGRAPH 2018 Talks*, 68:1–2.

[^3]: Barzel, R. 1997. Lighting controls for computer cinematography. *Journal of Graphics Tools* *2*  (1), 1–20.

[^4]: Bashford-Rogers, T., K. Debattista, and A. Chalmers. 2013. Importance driven environment map sampling. *IEEE Transactions on Visualization and Computer Graphics* *20*  (6), 907–18.

[^5]: Billen, N., A. Lagae, and P. Dutré. 2014. Probabilistic visibility evaluation using geometry proxies. *Computer Graphics Forum (Proceedings of the 2014 Eurographics Symposium on Rendering)* *33*  (4), 143–52.

[^6]: Billen, N., B. Engelen, A. Lagae, and P. Dutré. 2013. Probabilistic visibility evaluation for direct illumination. *Computer Graphics Forum (Proceedings of the 2013 Eurographics Symposium on Rendering)* *32*  (4), 39–47.

[^7]: Bitterli, B., C. Wyman, M. Pharr, P. Shirley, A. Lefohn, and W. Jarosz. 2020. Spatiotemporal reservoir resampling for real-time ray tracing with dynamic direct lighting. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *39*  (4), 148:1–17.

[^8]: Bitterli, B., J. Novák, and W. Jarosz. 2015. Portal-masked environment map sampling. *Computer Graphics Forum (Proceedings of the 2015 Eurographics Symposium on Rendering)* *34*  (4), 13–19.

[^9]: Bjorke, K. 2001. Using Maya with RenderMan on Final Fantasy: The Spirits Within. *SIGGRAPH 2001 RenderMan Course Notes.*

[^10]: Blinn, J. F., and M. E. Newell. 1976. Texture and reflection in computer generated images. *Communications of the ACM* *19*, 542–46.

[^11]: Cohen, J., A. Varshney, D. Manocha, G. Turk, H. Weber, P. Agarwal, F. P. Brooks Jr., and W. Wright. 1996. Simplification envelopes. In *Proceedings of SIGGRAPH ’96*, Computer Graphics Proceedings, Annual Conference Series, 119–28.

[^12]: Conty Estevez, A., and C. Kulla. 2018. Importance sampling of many lights with adaptive tree splitting. *Proceedings of the ACM on Computer Graphics and Interactive Techniques* *1*  (2), 25:1–17.

[^13]: Conty Estevez, A., and P. Lecocq. 2018. Fast product importance sampling of environment maps. *ACM SIGGRAPH 2018 Talks* 69, 1–2.

[^14]: Debevec, P. 1998. Rendering synthetic objects into real scenes: Bridging traditional and image-based graphics with global illumination and high dynamic range photography. In *Proceedings of SIGGRAPH ’98*, 189–98.

[^15]: Donikian, M., B. Walter, K. Bala, S. Fernandez, and D. P. Greenberg. 2006. Accurate direct illumination using iterative adaptive sampling. *IEEE Transactions on Visualization and Computer Graphics* *12*  (3), 353–64.

[^16]: Fernandez, S., K. Bala, and D. P. Greenberg. 2002. Local illumination environments for direct lighting acceleration. *Rendering Techniques 2002: 13th Eurographics Workshop on Rendering*, 7–14.

[^17]: Goesele, M., X. Granier, W. Heidrich, and H.-P. Seidel. 2003. Accurate light source acquisition and rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2003)* *22*  (3), 621–30.

[^18]: Greene, N. 1986. Environment mapping and other applications of world projections. *IEEE Computer Graphics and Applications* *6*  (11), 21–29.

[^19]: Guo, J. J., M. Eisemann, and E. Eisemann. 2020. Next event estimation++: Visibility mapping for efficient light transport simulation. *Computer Graphics Forum* *39*  (7), 205–17.

[^20]: Haines, E. A., and D. P. Greenberg. 1986. The light buffer: A shadow testing accelerator. *IEEE Computer Graphics and Applications* *6*  (9), 6–16.

[^21]: Hart, D., P. Dutré, and D. P. Greenberg. 1999. Direct illumination with lazy visibility evaluation. *Proceedings of SIGGRAPH ’99*, Computer Graphics Proceedings, Annual Conference Series, 147–54.

[^22]: Heidrich, W., J. Kautz, P. Slusallek, and H.-P. Seidel. 1998. Canned lightsources. In *Rendering Techniques ’98: Proceedings of the Eurographics Rendering Workshop*, 293–300.

[^23]: Illuminating Engineering Society of North America. 2002. IESNA standard file format for electronic transfer of photometric data. BSR/IESNA Publication LM-63-2002. [www.iesna.org.](http://www.iesna.org./)

[^24]: Karlík, O., M. Šik, P. Vévoda, T. Skřivan, and J. Křivánek. 2019. MIS compensation: Optimizing sampling techniques in multiple importance sampling. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *38*  (6), 151:1–12.

[^25]: Kider Jr., J. T., D. Knowlton, J. Newlin, Y. K. Li, and D. P. Greenberg. 2014. A framework for the experimental comparison of solar and skydome illumination. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2014)* *33*  (6), 180:1–12.

[^26]: Kniep, S., S. Häring, and M. Magnor. 2009. Efficient and accurate rendering of complex light sources. *Computer Graphics Forum (Proceedings of the 2009 Eurographics Symposium on Rendering)* *28*  (4), 1073–81.

[^27]: Laine, S. 2010. Restart trail for stackless BVH traversal. In *Proceedings of High Performance Graphics 2010*, 107–11.

[^28]: Lu, H., R. Pacanowski, and X. Granier. 2015. Position-dependent importance sampling of light field luminaires. *IEEE Transactions on Visualization and Computer Graphics* *21*  (2), 241–51.

[^29]: Lukaszewski, A. 2001. Exploiting coherence of shadow rays. In *AFRIGRAPH 2001*, 147–50. ACM SIGGRAPH.

[^30]: Mas, A., I. Martín, and G. Patow. 2008. Compression and importance sampling of near-field light sources. *Computer Graphics Forum* *27*  (8), 2013–27.

[^31]: Miller, G. S., and C. R. Hoffman. 1984. Illumination and reflection maps: Simulated objects in simulated and real environments. *Course Notes for Advanced Computer Graphics Animation, SIGGRAPH ’84.*

[^32]: Nishita, T., and E. Nakamae. 1986. Continuous tone representation of three-dimensional objects illuminated by sky light. In *Computer Graphics (Proceedings of SIGGRAPH ’86)*, Volume 20, 125–32.

[^33]: Ogaki, S. 2020. Generalized light portals. *Proceedings of the ACM on Computer Graphics and Interactive Techniques* *3*  (2), 10:1–19.

[^34]: Pearce, A. 1991. A recursive shadow voxel cache for ray tracing. In J. Arvo (ed.), *Graphics Gems II*, 273–74. San Diego: Academic Press.

[^35]: Popov, S., I. Georgiev, P. Slusallek, and C. Dachsbacher. 2013. Adaptive quantization visibility caching. *Computer Graphics Forum (Proceedings of Eurographics 2013)* *32*  (2), 399–408.

[^36]: Shirley, P., C. Y. Wang, and K. Zimmerman. 1996. Monte Carlo techniques for direct lighting calculations. *ACM Transactions on Graphics* *15*  (1), 1–36.

[^37]: Tokuyoshi, Y., and T. Harada. 2016. Stochastic light culling. *Journal of Computer Graphics Techniques (JCGT)* *5*  (1), 35–60.

[^38]: Unger, J., A. Wenger, T. Hawkins, A. Gardner, and P. Debevec. 2003. Capturing and rendering with incident light fields. In *Proceedings of the Eurographics Rendering Workshop 2003*, 141–49.

[^39]: Unger, J., S. Gustavson, P. Larsson, and A. Ynnerman. 2008. Free form incident light fields. *Computer Graphics Forum (Proceedings of the 2008 Eurographics Symposium on Rendering)* *27*  (4), 1293–1301.

[^40]: Vévoda, P., I. Kondapaneni, and J. Křivánek. 2018. Bayesian online regression for adaptive direct illumination sampling. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *37*  (4), 125:1–12.

[^41]: Velázquez-Armendáriz, E., Z. Dong, B. Walter, and D. P. Greenberg. 2015. Complex luminaires: Illumination and appearance rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2015)* *34*  (3), 26:1–15.

[^42]: Verbeck, C. P., and D. P. Greenberg. 1984. A comprehensive light source description for computer graphics. *IEEE Computer Graphics and Applications* *4*  (7), 66–75.

[^43]: Wald, I., C. Benthin, and P. Slusallek. 2003. Interactive global illumination in complex and highly occluded environments. In *Eurographics Symposium on Rendering: 14th Eurographics Workshop on Rendering*, 74–81.

[^44]: Ward, G. 1991. Adaptive shadow testing for ray tracing. In *Second Eurographics Workshop on Rendering*

[^45]: Warn, D. R. 1983. Lighting controls for synthetic images. In *Computer Graphics (Proceedings of SIGGRAPH ’83)*, Volume 17, 13–21.

[^46]: Woo, A., and J. Amanatides. 1990. Voxel occlusion testing: A shadow determination accelerator for ray tracing. In *Proceedings of Graphics Interface 1990*, 213–20.

[^47]: Zhu, J., Y. Bai, Z. Xu, S. Bako, E. Velázquez-Armendáriz, L. Wang, P. Sen, M. Hašan, and L.-Q. Yan. 2021. Neural complex luminaires: Representation and rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 57:1–12.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[光源]]

**同章节**:
- [[12 Light Sources]]
- [[12.1 Light Interface]]
- [[12.2 Point Lights]]
- [[12.3 Distant Lights]]
- [[12.4 Area Lights]]
- [[12.5 Infinite Area Lights]]
- [[12.6 Light Sampling]]
