---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Textures_and_Materials/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
#### Ray Footprints

The cone-tracing method of Amanatides ([^2]) was one of the first techniques for automatically estimating filter footprints for ray tracing. The beam-tracing algorithm of Heckbert and Hanrahan ([^36]) was another early extension of ray tracing to incorporate an area associated with each image sample rather than just an infinitesimal ray. The pencil-tracing method of Shinya et al. ([^70]) is another approach to this problem. Other related work on the topic of associating areas or footprints with rays includes Mitchell and Hanrahan’s paper ([^58]) on rendering caustics and Turkowski’s technical report ([^77]).

Collins ([^14]) estimated the ray footprint by keeping a tree of all rays traced from a given camera ray, examining corresponding rays at the same level and position. The ray differentials used in pbrt are based on Igehy’s ([^40]) formulation, which was extended by Suykens and Willems ([^74]) to handle glossy reflection in addition to perfect specular reflection. Belcour et al. ([^7]) applied Fourier analysis to the light transport equation in order to accurately and efficiently track ray footprints after scattering.

Twelve floating-point values are required to store ray differentials, and Belcour et al.’s approach has similar storage requirements. This poses no challenge in a CPU ray tracer that only operates on one or a few rays at a time, but can add up to a considerable amount of storage (and consequently, bandwidth consumption) on the GPU. To address this issue, Akenine-Möller et al. ([^1]) developed a number of more space-efficient alternatives and showed their effectiveness for antialiasing that was further improved in subsequent work ([Akenine-Möller et al. 2021](#cite:AkenineMoller2021); [Boksansky et al. 2021](#cite:Boksansky2021)). The approach we have implemented in [CameraBase::Approximate\_dp\_dxy()](https://pbr-book.org/4ed/Textures_and_Materials/Texture_Sampling_and_Antialiasing.html#CameraBase::Approximate_dp_dxy) was described by Li ([^51]).

Worley’s chapter in *Texturing and Modeling* (Ebert et al. [^22]) on computing differentials for filter regions presents an approach similar to ours. See Elek et al. ([^23]) for an extension of ray differentials to include wavelength, which can improve results with spectral rendering.

#### Image Texture Maps

Two-dimensional texture mapping with images was first introduced to graphics by Blinn and Newell ([^9]). Ever since Crow ([^16]) identified aliasing as the source of many errors in images in graphics, much work has been done to find efficient and effective ways of antialiasing image maps. Dungan, Stenger, and Sutty ([^20]) were the first to suggest creating a pyramid of prefiltered texture images; they used the nearest texture sample at the appropriate level when looking up texture values, using supersampling in screen space to antialias the result. Feibush, Levoy, and Cook ([^24]) investigated a spatially varying filter function, rather than a simple box filter. (Blinn and Newell were aware of Crow’s results and used a box filter for their textures.)

Williams ([^81]) used a MIP map image pyramid for texture filtering with trilinear interpolation. Shortly thereafter, Crow ([^17]) introduced summed area tables, which make it possible to efficiently filter over axis-aligned rectangular regions of texture space. Summed area tables handle anisotropy better than Williams’s method, although only for primarily axis-aligned filter regions. Heckbert ([^35]) wrote a good survey of early texture mapping algorithms.

Greene and Heckbert ([^35]) originally developed the elliptically weighted average technique, and Heckbert’s master’s thesis ([1989b](#cite:Heckbert89)) put the method on a solid theoretical footing. Fournier and Fiume ([^27]) developed an even higher-quality texture filtering method that focuses on using a bounded amount of computation per lookup. Nonetheless, their method appears to be less efficient than EWA overall. Lansdale’s master’s thesis ([^50]) has an extensive description of EWA and Fournier and Fiume’s method, including implementation details.

A number of researchers have investigated generalizing Williams’s original method using a series of trilinear MIP map samples in an effort to increase quality without having to pay the price for the general EWA algorithm. By taking multiple samples from the MIP map, anisotropy is handled well while preserving the computational efficiency. Examples include Barkans’s ([^4]) description of texture filtering in the Talisman architecture, McCormack et al.’s ([^56]) Feline method, and Cant and Shrubsole’s ([^12]) technique. Manson and Schaefer ([^53], [^54]) have shown how to accurately approximate a variety of filter functions with a fixed small number of bilinearly interpolated sample values. An algorithm to convert an arbitrary filter into a set of bilinear lookups over multiple passes subject to a specified performance target was given by Schuster et al. ([^69]). These sorts of approaches are particularly useful on GPUs, where hardware-accelerated bilinear interpolation is available.

For scenes with many image textures where reading them all into memory simultaneously has a prohibitive memory cost, an effective approach can be to allocate a fixed amount of memory for image maps (a *texture cache*), load textures into that memory on demand, and discard the image maps that have not been accessed recently when the memory fills up ([Peachey 1990](#cite:Peachey90)). To enable good performance with small texture caches, image maps should be stored in a *tiled* format that makes it possible to load in small square regions of the texture independently of each other. Tiling techniques like these are used in graphics hardware to improve the performance of their texture memory caches ([Hakura and Gupta 1997](#cite:Hakura97); Igehy et al. [^41], [^42]). High-performance texture caching with parallel execution can be challenging because the cache contents may be frequently updated; it is desirable to minimize mutual exclusion in the cache implementation so that threads do not stall while others are updating the cache. For an effective approach to this problem, see Pharr ([^62]), who applied the *read-copy update* technique ([McKenney and Slingwine 1998](#cite:McKenney1998)) to accomplish this.

Smith’s ([^71]) website and document on audio resampling gives a good overview of resampling signals in one dimension. Heckbert’s ([1989a](#cite:HeckbertZoom)) zoom source code is the canonical reference for image resampling. His implementation carefully avoids feedback without using auxiliary storage.

A variety of *texture synthesis* algorithms have been developed that take an example texture image and then synthesize larger texture images that appear similar to the original texture while not being exactly the same. Survey articles by Wei et al. ([^80]) and Barnes and Zhang ([^5]) summarize work in this area. Convolutional neural networks have been applied to this task ([Gatys et al. 2015](#cite:Gatys2015); [Sendik and Cohen-Or 2017](#cite:Sendik2017)), giving impressive results, and Frühstück et al. ([^28]) have showed the effectiveness of generative adversarial networks for this problem.

#### Solid Texturing and Noise Functions

Three-dimensional solid texturing was originally developed by Gardner ([^29], [^30]), Perlin ([1985a](#cite:Perlin85)), and Peachey ([^61]). Norton, Rockwood, and Skolmoski ([^59]) developed the *clamping* method that is widely used for antialiasing textures based on solid texturing. The general idea of procedural texturing, where texture is generated via computation rather than via looking up values from images, was introduced by Cook ([^15]), Perlin ([1985a](#cite:Perlin85)), and Peachey ([^61]).

*Noise functions*, which randomly vary while still having limited frequency content, have been a key ingredient for many procedural texturing techniques. Perlin ([1985a](#cite:Perlin85)) introduced the first such noise function, and later revised it to correct a number of subtle shortcomings ([Perlin 2002](#cite:Perlin02)). (See also Kensler et al. ([^47]) for further improvements.) Many more noise functions have been developed; see Lagae et al. ([^49]) for a survey of work up to that year. Tricard et al. ([^76]) recently introduced a noise function (“phasor noise”) that can be filtered anisotropically and allows control of the orientation, frequency, and contrast of the noise function. Their paper also includes citations to other recent work on this topic.

In recent years, the *Shadertoy* website, [shadertoy.com](http://shadertoy.com/), has become a hub of creative application of procedural modeling and texturing, all of it running interactively in web browsers. *Shadertoy* was developed by Quilez and Jeremias ([^65]).

#### Shading Languages

The first languages and systems that supported the idea of user-supplied procedural shaders were developed by Cook ([^15]) and Perlin ([1985a](#cite:Perlin85)). (The texture composition model in this chapter is similar to Cook’s shade trees.) The *RenderMan* shading language, described in a paper by Hanrahan and Lawson ([^33]), remains the classic shading language in graphics, though a more modern shading language is available in *Open Shading Language* (OSL) (Gritz et al. [^31]), which is open source and increasingly used for production rendering. It follows pbrt ’s model of the shader returning a representation of the material rather than a final color value. See also Karrenberg et al. ([^46]), who introduced the *AnySL* shading language, which was designed for high performance as well as portability across multiple rendering systems (including pbrt).

See Ebert et al. ([^22]) and Apodaca and Gritz ([^3]) for techniques for writing procedural shaders; both of those have excellent discussions of issues related to antialiasing in procedural shaders.

#### Normal Mapping, Bump Mapping, and Shading Normals

Blinn ([^8]) invented the bump-mapping technique. Kajiya ([^44]) generalized the idea of bump mapping the normal to *frame mapping*, which also perturbs the surface’s primary tangent vector and is useful for controlling the appearance of anisotropic reflection models. Normal mapping was introduced by Cohen et al. ([^13]).

Mikkelsen’s thesis ([^57]) carefully investigates a number of the assumptions underlying bump mapping and normal mapping, proposes generalizations, and addresses a number of subtleties with respect to its application to real-time rendering.

One visual shortcoming of normal and bump mapping is that those techniques do not naturally account for self-shadowing, where bumps cast shadows on the surface and prevent light from reaching nearby points. These shadows can have a significant impact on the appearance of rough surfaces. Max ([^55]) developed the *horizon mapping* technique, which efficiently accounts for this effect through precomputed information about each bump map. More recently, Conty Estevez et al. and Chiang et al. have introduced techniques based on microfacet shadowing functions to improve the visual fidelity of bump-mapped surfaces at shadow terminators ([Conty Estevez et al. 2019](#cite:ContyEstevez2019), [Chiang et al. 2019](#cite:Chiang2019)).

Another challenging issue is that antialiasing bump and normal maps that have higher-frequency detail than can be represented in the image is quite difficult. In particular, it is not enough to remove high-frequency detail from the underlying function, but in general the BSDF needs to be modified to account for this detail. Fournier ([^26]) applied normal distribution functions to this problem, where the surface normal was generalized to represent a distribution of normal directions. Becker and Max ([^6]) developed algorithms for blending between bump maps and BRDFs that represented higher-frequency details. Schilling ([^67], [^68]) investigated this issue particularly for application to graphics hardware.

Effective approaches to filtering bump maps were developed by Han et al. ([^32]) and Olano and Baker ([^60]). Both Dupuy et al. ([^21]) and Hery et al. ([^39]) developed techniques that convert displacements into anisotropic distributions of Beckmann microfacets. Further improvements to these approaches were introduced by Kaplanyan et al. ([^45]), Tokuyoshi and Kaplanyan ([^75]), and Wu et al. ([^82]).

A number of researchers have looked at the issue of antialiasing surface reflection functions. Early work in this area was done by Amanatides, who developed an algorithm to detect specular aliasing for a specific BRDF model ([Amanatides 1992](#cite:Amanatides92)). Van Horn and Turk ([^78]) developed an approach to automatically generate MIP maps of reflection functions that represent the characteristics of shaders over finite areas in order to antialias them. Bruneton and Neyret ([^10]) surveyed the state of the art in this area, and Jarabo et al. ([2014b](#cite:Jarabo2014b)) also considered perceptual issues related to filtering inputs to these functions. See also Heitz et al. ([^38]) for further work on this topic.

#### Displacement Mapping

An alternative to bump mapping is displacement mapping, where the bump function is used to actually modify the surface geometry, rather than just perturbing the normal ([^15]; [Cook et al. 1987](#cite:Cook87)). Advantages of displacement mapping include geometric detail on object silhouettes and the possibility of accounting for self-shadowing. Patterson and collaborators described an innovative algorithm for displacement mapping with ray tracing where the geometry is unperturbed, but the ray’s direction is modified such that the intersections that are found are the same as would be found with the displaced geometry ([Patterson et al. 1991](#cite:Patterson91); [Logie and Patterson 1994](#cite:Logie94)). Heidrich and Seidel ([^37]) developed a technique for computing direct intersections with procedurally defined displacement functions.

One approach for displacement mapping has been to use an implicit function to define the displaced surface and to then take steps along rays until a zero crossing with the implicit function is found—this point is an intersection. This approach was first introduced by Hart ([^34]); see Donnelly ([^19]) for information about using this approach for displacement mapping on the GPU. (This approach was more recently popularized by Quilez ([^64]) on the *Shadertoy* website.)

Another option is to finely tessellate the scene geometry and displace its vertices to define high-resolution meshes. Pharr and Hanrahan ([^63]) described an approach to this problem based on geometry caching, and Wang et al. ([^79]) described an adaptive tessellation algorithm that reduces memory requirements. Smits, Shirley, and Stark ([^72]) lazily tessellate individual triangles, saving a substantial amount of memory.

Measuring fine-scale surface geometry of real surfaces to acquire bump or displacement maps can be challenging. Johnson et al. ([^43]) developed a novel handheld system that can measure detail down to a few microns, which more than suffices for these uses.

#### Material Models

Burley’s ([^11]) course notes describe a material model developed at Disney for feature films. This write-up includes extensive discussion of features of real-world reflection functions that can be observed in Matusik et al.’s ([2003b](#cite:Matusik03b)) measurements of one hundred BRDFs and analyzes the ways that existing BRDF models do and do not fit these features well. These insights are then used to develop an “artist-friendly” material model that can express a wide range of surface appearances. The model describes reflection with a single color and ten scalar parameters, all of which are in the range and have fairly predictable effects on the appearance of the resulting material. An earlier material model designed to have intuitive parameters for artistic control was developed by Strauss ([^73]).

The *bidirectional texture function* (BTF) is a generalization of the BRDF that was introduced by Dana et al. ([^18]). (BTFs are also referred to as spatially varying BRDFs (SVBRDFs).) It is a six-dimensional reflectance function that adds two dimensions to account for spatial variation to the BSDF. pbrt ’s material model can thus be seen as imposing a particular factorization of the BTF where variation due to the spatial dimension is incorporated into textures that in turn provide values for a parametric BSDF that defines the directional distribution. The BTF representation is especially useful for material acquisition, as it does not impose a particular representation or specific factorization of the six dimensions. The survey articles on BTF acquisition and representation by Müller et al. ([^52]) and Filip and Haindl ([^25]) have good coverage of earlier work in this area.

Rainer et al. ([^66]) recently trained a neural network to represent a given BTF; network evaluation took the position and lighting directions as parameters and returned the corresponding BTF value. This work was subsequently generalized with a technique based on training a single network that provides a parameterization to which given BTFs can easily be mapped ([Rainer et al. 2020](#cite:Rainer2020)). Kuznetsov et al. ([^48]) also used a neural approach, developing a compact representation that allowed 7D queries of position, two directions, and a filter size.

### References

[^1]: Akenine-Möller, T., J. Nilsson., M. Andersson, C. Barré-Brisebois, R. Toth, and T. Karras. 2019. Texture level of detail strategies for real-time ray tracing. In E. Haines and T. Akenine-Möller (ed.), *Ray Tracing Gems,* 321–45. Berkeley: Apress.

[^2]: Amanatides, J. 1984. Ray tracing with cones. *Computer Graphics (SIGGRAPH ’84 Proceedings)* *18*  (3), 129–35.

[^3]: Apodaca, A. A., and L. Gritz. 2000. *Advanced RenderMan: Creating CGI for Motion Pictures.* San Francisco: Morgan Kaufmann.

[^4]: Barkans, A. C. 1997. High-quality rendering using the Talisman architecture. In *1997 SIGGRAPH/Eurographics Workshop on Graphics Hardware*, 79–88.

[^5]: Barnes, C., and F.-L. Zhang. 2017. A survey of the state-of-the-art in patch-based synthesis. *Computational Visual Media* *3*, 3–20.

[^6]: Becker, B. G., and N. L. Max. 1993. Smooth transitions between bump rendering algorithms. In *Proceedings of SIGGRAPH ’93*, Computer Graphics Proceedings, Annual Conference Series, 183–90.

[^7]: Belcour, L., L.-Q. Yan, R. Ramamoorthi, and D. Nowrouzezahrai. 2017. Antialiasing complex global illumination effects in path-space. *ACM Transactions on Graphics* *36*  (1), 9:1–13.

[^8]: Blinn, J. F. 1978. Simulation of wrinkled surfaces. In *Computer Graphics (SIGGRAPH ’78 Proceedings)* *12*, 286–92.

[^9]: Blinn, J. F., and M. E. Newell. 1976. Texture and reflection in computer generated images. *Communications of the ACM* *19*, 542–46.

[^10]: Bruneton, E., and F. Neyret. 2012. A survey of nonlinear prefiltering methods for efficient and accurate surface shading. *IEEE Transactions on Visualization and Computer Graphics* *18*  (2), 242–60.

[^11]: Burley, B. 2012. Physically-based shading at Disney. *Physically Based Shading in Film and Game Production, SIGGRAPH 2012 Course Notes*

[^12]: Cant, R. J., and P. A. Shrubsole. 2000. Texture potential MIP mapping, a new high-quality texture antialiasing algorithm. *ACM Transactions on Graphics* *19*  (3), 164–84.

[^13]: Cohen, J., M. Olano, and D. Manocha. 1998. Appearance-preserving simplification. In *Proceedings of SIGGRAPH ’98*, Computer Graphics Proceedings, Annual Conference Series, 115–22.

[^14]: Collins, S. 1994. Adaptive splatting for specular to diffuse light transport. In *Fifth Eurographics Workshop on Rendering*, 119–35.

[^15]: Cook, R. L. 1984. Shade trees. *Computer Graphics (SIGGRAPH ’84 Proceedings)* *18*, 223–31.

[^16]: Crow, F. C. 1977. The aliasing problem in computer-generated shaded images. *Communications of the ACM* *20*  (11), 799–805.

[^17]: Crow, F. C. 1984. Summed-area tables for texture mapping. *Computer Graphics (Proceedings of SIGGRAPH ’84)* *18*, 207–12.

[^18]: Dana, K. J., B. van Ginneken, S. K. Nayar, and J. J. Koenderink. 1999. Reflectance and texture of real-world surfaces. *ACM Transactions on Graphics* *18*  (1), 1–34.

[^19]: Donnelly, W. 2005. Per-pixel displacement mapping with distance functions. In M. Pharr (ed.), *GPU Gems 2*, 123–35. Reading, Massachusetts: Addison-Wesley.

[^20]: Dungan, W. Jr., A. Stenger, and G. Sutty. 1978. Texture tile considerations for raster graphics. *Computer Graphics (Proceedings of SIGGRAPH ’78)* *12*, 130–34.

[^21]: Dupuy, J., E. Heitz, J.-C. Iehl, P. Poulin, F. Neyret, and V. Ostromoukhov. 2013. Linear efficient antialiased displacement and reflectance mapping. *ACM Transactions on Graphics* *32*  (6), 211:1–11.

[^22]: Ebert, D., F. K. Musgrave, D. Peachey, K. Perlin, and S. Worley. 2003. *Texturing and Modeling: A Procedural Approach*. San Francisco: Morgan Kaufmann.

[^23]: Elek, O., P. Bauszat, T. Ritschel, M. Magnor, and H.-P. Seidel. Spectral ray differentials. 2014. *Computer Graphics Forum (Proceedings of the 2014 Eurographics Symposium on Rendering)* *33*  (4), 113–22.

[^24]: Feibush, E. A., M. Levoy, and R. L. Cook. 1980. Synthetic texturing using digital filters. *Computer Graphics (Proceedings of SIGGRAPH ’80)* *14*, 294–301.

[^25]: Filip, J., and M. Haindl. 2009. Bidirectional texture function modeling: A state of the art survey. *IEEE Transactions on Pattern Analysis and Machine Intelligence* *31*  (11), 1921–40.

[^26]: Fournier, A. 1992. Normal distribution functions and multiple surfaces. *Graphics Interface ’92 Workshop on Local Illumination*, 45–52.

[^27]: Fournier, A., and E. Fiume. 1988. Constant-time filtering with space-variant kernels. *Computer Graphics (SIGGRAPH ’88 Proceedings)* *22*  (4), 229–38.

[^28]: Frühstück, A., I. Alhashim, and P. Wonka. 2019. TileGAN: Synthesis of large-scale non-homogeneous textures. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *38*  (4), 58:1–11.

[^29]: Gardner, G. Y. 1984. Simulation of natural scenes using textured quadric surfaces. *Computer Graphics (SIGGRAPH ’84 Proceedings)* *18*  (3), 11–20.

[^30]: Gardner, G. Y. 1985. Visual simulation of clouds. *Computer Graphics (Proceedings of SIGGRAPH ’85)* *19*, 297–303.

[^31]: Gritz, L., C. Stein, C. Kulla, and A. Conty. 2010. Open Shading Language. *SIGGRAPH 2010 Talks*, 3:1.

[^32]: Han, C., B. Sun, R. Ramamoorthi, and E. Grinspun. 2007. Frequency domain normal map filtering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2007)* *26*  (3), 28:1–11.

[^33]: Hanrahan, P., and J. Lawson. 1990. A language for shading and lighting calculations. *Computer Graphics (SIGGRAPH ’90 Proceedings)* *24*, 289–98.

[^34]: Hart, J. C. 1996. Sphere tracing: A geometric method for the antialiased ray tracing of implicit surfaces. *The Visual Computer* *12*  (9), 527–45.

[^35]: Heckbert, P. S. 1986. Survey of texture mapping. *IEEE Computer Graphics and Applications* *6*  (11), 56–67.

[^36]: Heckbert, P. S., and P. Hanrahan. 1984. Beam tracing polygonal objects. In *Computer Graphics (Proceedings of SIGGRAPH ’84)* *18*, 119–27.

[^37]: Heidrich, W., and H.-P. Seidel. 1998. Ray-tracing procedural displacement shaders. In *Proceedings of Graphics Interface 1998*, 8–16.

[^38]: Heitz, E., D. Nowrouzezahrai, P. Poulin, and F. Neyret. 2014. Filtering non-linear transfer functions on surfaces. *IEEE Transactions on Visualization and Computer Graphics* *20*  (7), 996–1008.

[^39]: Hery, C., M. Kass, and J. Ling. 2014. Geometry into shading. *Pixar Technical Memo 14-04*

[^40]: Igehy, H. 1999. Tracing ray differentials. In *Proceedings of SIGGRAPH ’99*, Computer Graphics Proceedings, Annual Conference Series, 179–86.

[^41]: Igehy, H., M. Eldridge, and K. Proudfoot. 1998. Prefetching in a texture cache architecture. In *1998 SIGGRAPH/Eurographics Workshop on Graphics Hardware*, 133–42.

[^42]: Igehy, H., M. Eldridge, and P. Hanrahan. 1999. Parallel texture caching. In *1999 SIGGRAPH/Eurographics Workshop on Graphics Hardware*, 95–106.

[^43]: Johnson, M. K., F. Cole, A. Raj, and E. H. Adelson. 2011. Microgeometry capture using an elastomeric sensor. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2011)* *30*  (4), 46:1–8.

[^44]: Kajiya, J. T. 1985. Anisotropic reflection models. *Computer Graphics (Proceedings of SIGGRAPH ’85)* *19*, 15–21.

[^45]: Kaplanyan, A. S., S. Hill, A. Patney, and A. Lefohn. 2016. Filtering distributions of normals for shading antialiasing. In *Proceedings of High Performance Graphics (HPG ’16)*

[^46]: Karrenberg, R., D. Rubinstein, P. Slusallek, and S. Hack. 2010. AnySL: Efficient and portable shading for ray tracing. In *Proceedings of High Performance Graphics 2010*, 97–105.

[^47]: Kensler, A., A. Knoll, and P. Shirley. 2008. Better gradient noise. *Technical Report UUSCI-2008-001*, SCI Institute, University of Utah.

[^48]: Kuznetsov, A., K. Mullia, Z. Xu, M. Hašan, and R. Ramamoorthi. 2021. NeuMIP: Multi-resolution neural materials. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 175:1–13.

[^49]: Lagae, A., S. Lefebvre, R. Cook, T. DeRose, G. Drettakis, D. S. Ebert, J. P. Lewis, K. Perlin, and M. Zwicker. 2010. A survey of procedural noise functions. *Computer Graphics Forum* *29*  (8), 2579–600.

[^50]: Lansdale, R. C. 1991. Texture mapping and resampling for computer graphics. M.S. thesis, Department of Electrical Engineering, University of Toronto.

[^51]: Li, Y.-K. 2018. Mipmapping with bidirectional techniques. [https://blog.yiningkarlli.com/2018/10/bidirectional-mipmap.html](https://blog.yiningkarlli.com/2018/10/bidirectional-mipmap.html)

[^52]: Müller, G., J. Meseth, M. Sattler, R. Sarlette, and R. Klein. 2005. Acquisition, synthesis and rendering of bidirectional texture functions. *Computer Graphics Forum (Eurographics State of the Art Report)* *24*  (1), 83–109.

[^53]: Manson, J., and S. Schaefer. 2013. Cardinality-constrained texture filtering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2013)* *32*  (4), 140:1–8.

[^54]: Manson, J., and S. Schaefer. 2014. Bilinear accelerated filter approximation. *Computer Graphics Forum (Proceedings of the 2014 Eurographics Symposium on Rendering)* *33*  (4), 33–40.

[^55]: Max, N. L. 1988. Horizon mapping: Shadows for bump-mapped surfaces. *The Visual Computer* *4*  (2), 109–17.

[^56]: McCormack, J., R. Perry, K. I. Farkas, and N. P. Jouppi. 1999. Feline: Fast elliptical lines for anisotropic texture mapping. In *Proceedings of SIGGRAPH ’99*, Computer Graphics Proceedings, Annual Conference Series, 243–50.

[^57]: Mikkelsen, M. 2008. Simulation of wrinkled surfaces revisited. M.S. thesis, University of Copenhagen.

[^58]: Mitchell, D. P., and P. Hanrahan. 1992. Illumination from curved reflectors. In *Computer Graphics (Proceedings of SIGGRAPH ’92)*, Volume 26, 283–91.

[^59]: Norton, A., A. P. Rockwood, and P. T. Skolmoski. 1982. Clamping: A method of antialiasing textured surfaces by bandwidth limiting in object space. In *Computer Graphics (Proceedings of SIGGRAPH ’82)*, Volume 16, 1–8.

[^60]: Olano, M., and D. Baker. 2010. LEAN mapping. In *Proceedings of the 2010 ACM SIGGRAPH Symposium on Interactive 3D Graphics and Games*, 181–88.

[^61]: Peachey, D. R. 1985. Solid texturing of complex surfaces. *Computer Graphics (SIGGRAPH ’85 Proceedings)*, Volume 19, 279–86.

[^62]: Pharr, M. 2017. The implementation of a scalable texture cache. [https://www.pbrt.org/texcache.pdf](https://www.pbrt.org/texcache.pdf)

[^63]: Pharr, M., and P. Hanrahan. 1996. Geometry caching for ray-tracing displacement maps. In *Eurographics Rendering Workshop 1996*, 31–40.

[^64]: Quilez, I. 2015. Distance estimation. [http://iquilezles.org/www/articles/distance/distance.htm](http://iquilezles.org/www/articles/distance/distance.htm)

[^65]: Quilez, I., and P. Jeremias. 2021. Shadertoy. [https://shadertoy.com](https://shadertoy.com/)

[^66]: Rainer, G., W. Jakob, A. Ghosh, and T. Weyrich. 2019. Neural BTF compression and interpolation. *Computer Graphics Forum* *38*  (2), 235–44.

[^67]: Schilling, A. 1997. Toward real-time photorealistic rendering: Challenges and solutions. In *1997 SIGGRAPH/Eurographics Workshop on Graphics Hardware*, 7–16.

[^68]: Schilling, A. 2001. Antialiasing of environment maps. *Computer Graphics Forum* *20*  (1), 5–11.

[^69]: Schuster, K., P. Trettner, and L. Kobbelt. 2020. High-performance image filters via sparse approximations. *Proceedings of the ACM on Computer Graphics and Interactive Techniques* *3*  (2), 14:1–19.

[^70]: Shinya, M., T. Takahashi, and S. Naito. 1987. Principles and applications of pencil tracing. In *Computer Graphics (Proceedings of SIGGRAPH ’87)*, Volume 21, 45–54.

[^71]: Smith, J. O. 2002. Digital audio resampling home page. [http://ccrma.stanford.edu/ jos/resample/](http://ccrma.stanford.edu/%C2%A0jos/resample/)

[^72]: Smits, B., P. S. Shirley, and M. M. Stark. 2000. Direct ray tracing of displacement mapped triangles. In *Rendering Techniques 2000: 11th Eurographics Workshop on Rendering*, 307–18.

[^73]: Strauss, P. S. 1990. A realistic lighting model for computer animators. *IEEE Computer Graphics and Applications* *10*  (6), 56–64.

[^74]: Suykens, F., and Y. Willems. 2001. Path differentials and applications. In *Rendering Techniques 2001: 12th Eurographics Workshop on Rendering*, 257–68.

[^75]: Tokuyoshi, Y., and A. S. Kaplanyan. 2019. Improved geometric specular antialiasing. *Proceedings of the ACM SIGGRAPH Symposium on Interactive 3D Graphics and Games (I3D ’19)*, 8:1–8.

[^76]: Tricard, T., S. Efremov, C. Zanni, F. Neyret, J. Martínez, and S. Lefebvre. 2019. Procedural phasor noise. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *38*  (4), 57:1–15.

[^77]: Turkowski, K. 1993. The differential geometry of texture-mapping and shading. *Technical Note*, Advanced Technology Group, Apple Computer.

[^78]: Van Horn, B., and G. Turk. 2008. Antialiasing procedural shaders with reduction maps. *IEEE Transactions on Visualization and Computer Graphics* *14*  (3), 539–50.

[^79]: Wang, X. C., J. Maillot, E. L. Fiume, V. Ng-Thow-Hing, A. Woo, and S. Bakshi. 2000. Feature-based displacement mapping. In *Rendering Techniques 2000: 11th Eurographics Workshop on Rendering*, 257–68.

[^80]: Wei, L.-Y., S. Lefebvre, V. Kwatra, and G. Turk. 2009. State of the art in example-based texture synthesis. In *Eurographics 2009, State of the Art Report*

[^81]: Williams, L. 1983. Pyramidal parametrics. In *Computer Graphics (SIGGRAPH ’83 Proceedings)*, Volume 17, 1–11.

[^82]: Wu, L., S. Zhao, L.-Q. Yan, and R. Ramamoorthi. 2019. Accurate appearance preserving prefiltering for rendering displacement-mapped surfaces. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *38*  (4), 137:1–14.