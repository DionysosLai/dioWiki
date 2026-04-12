---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Reflection_Models/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
Hall’s ([^40]) book collected and described the state of the art in physically based surface reflection models for graphics; it remains a seminal reference. It discusses the physics of surface reflection in detail, with many pointers to the original literature.

#### Microfacet Models

Phong ([^70]) developed an early empirical reflection model for glossy surfaces in computer graphics. Although neither reciprocal nor energy conserving, it was a cornerstone of the first synthetic images of non-Lambertian objects. The Torrance–Sparrow microfacet model was described by Torrance and Sparrow ([^88]); it was first introduced to graphics by Blinn ([^12]), and a variant of it was used by Cook and Torrance ([^18], [^19]).

The papers by Beckmann and Spizzichino ([^8]) and Trowbridge and Reitz ([^89]) introduced two widely used microfacet distribution functions. Kurt et al. ([^55]) introduced an anisotropic variant of the Beckmann–Spizzichino distribution function; see Heitz ([^42]) for anisotropic variants of many other microfacet distribution functions. (Early anisotropic BRDF models for computer graphics were developed by Kajiya ([^50]) and Poulin and Fournier ([^71]).) Ribardière et al. ([^74]) applied Student’s t-distribution to model microfacet distributions; it provides an additional degree of freedom, which they showed allows a better fit to measured data while subsuming both the Beckmann–Spizzichino and Trowbridge–Reitz distributions. Ribardière et al. ([^75]) investigated the connection between normal distribution functions (NDFs) and microfacet distributions and also showed how to generate surfaces with distributions described by their model.

The microfacet masking-shadowing function was introduced by Smith ([^80]), building on the assumption that heights of nearby points on the microfacet surface are uncorrelated. Smith also first derived the normalization constraint in Equation ([9.17](https://pbr-book.org/4ed/Reflection_Models/Roughness_Using_Microfacet_Theory.html#eq:microfacet-g1-condition)). Heitz’s paper on microfacet masking-shadowing functions ([^42]) provides a very well-written introduction to microfacet BSDF models in general, with many useful figures and explanations about details of the topic.

The more accurate function for Gaussian microfacet surfaces that better accounts for the effects of correlation between the two directions that we have implemented is due to Ross et al. ([^76]). Our derivation of the function, Equation ([9.19](https://pbr-book.org/4ed/Reflection_Models/Roughness_Using_Microfacet_Theory.html#eq:microfacet-lambda)), follows Heitz ([^43]).

For many decades, Monte Carlo rendering of microfacet models involved generating samples proportional to the microfacet distribution. Heitz and d’Eon ([^45]) were the first to demonstrate that it was possible to reduce variance by restricting this sampling process to only consider visible microfacets. Our microfacet sampling implementation in Section [9.6](https://pbr-book.org/4ed/Reflection_Models/Roughness_Using_Microfacet_Theory.html#sec:microfacet) follows Heitz’s improved approach ([^44]), which showed that sampling the visible area of the Trowbridge–Reitz microfacet distribution corresponds to sampling the projection of a truncated ellipsoid, which in turn can be performed using an approach developed by Walter et al. ([^95]). See also Heitz ([^42]) for an overview of traditional techniques that directly sample the microfacet distribution without considering visibility.

When dealing with refraction through rough dielectrics, a modified change of variables term is needed to account for the mapping from half vectors to outgoing direction. A model based on this approach was originally developed by Stam ([^83]); Walter et al. ([^94]) proposed improvements and provided an elegant geometric justification of the half vector mapping of Equation ([9.36](https://pbr-book.org/4ed/Reflection_Models/Rough_Dielectric_BSDF.html#eq:mf-transmission-change-of-vars)). The generalized half-direction vector for refraction used in these models and in Equation ([9.34](https://pbr-book.org/4ed/Reflection_Models/Rough_Dielectric_BSDF.html#eq:wm-microfacet-transmission)) is due to Sommerfeld and Runge ([^81]).

One issue with the specular term of the Torrance–Sparrow BRDF presented in Section [9.6.5](https://pbr-book.org/4ed/Reflection_Models/Roughness_Using_Microfacet_Theory.html#sec:torrance-sparrow) is that it only models a single scattering interaction with the microfacet surface, causing a growing portion of the energy to be lost as the roughness increases. In scenes where many subsequent interactions are crucial (e.g., a complex 3D object made from a translucent rough dielectric material), this energy loss can become so conspicuous that standard microfacet models become effectively unusable.

The original model by Torrance and Sparrow ([^88]) included a diffuse component to simulate light having scattered multiple times. However, a simple diffuse correction is generally unsatisfactory, since the precise amount of energy loss will depend both on the surface roughness and the angle of incidence. Kelemen and Szirmay-Kalos ([^52]) proposed an improved diffuse-like term that accounts for this dependence. Jakob et al. ([2014a](#cite:Jakob2014a)) generalized their approach to rough dielectric boundaries in the context of layered structures, where energy losses can be particularly undesirable.

In all of these cases, light is treated as essentially diffuse following scattering by multiple facets. Building on Smith’s uncorrelated height assumption, Heitz et al. ([2016b](#cite:Heitz2016:smith-multi)) cast a microfacet BRDF model into a volumetric analogue composed of microflakes—that is, a distribution of mirror facets suspended in a 3D space. With this new interpretation, they are able to simulate an arbitrary number of volumetric scattering interactions to evaluate an effective BRDF that is free of energy loss and arguably closer to physical reality.

Analytic solutions may sometimes obviate the need for a stochastic simulation of interreflection. For example, Lee et al. ([^57]) and Xie and Hanrahan ([^105]) both derived analytic models for multiple scattering under the assumption of microfacets with a v-groove shape. Efficient approximate models for multiple scattering among microfacets were presented by Kulla and Conty Estevez ([^54]) and by Turquin ([^90]).

Microfacet models have provided a foundation for a variety of additional reflection models. Simonot ([^79]) has developed a model that spans Oren–Nayar’s diffuse microfacet model ([^66]) and Torrance–Sparrow: microfacets are modeled as Lambertian reflectors with a layer above them that ranges from perfectly transmissive to a perfect specular reflector. Conty Estevez and Kulla ([^17]) have developed a model for cloth. The halo of a softer and wider secondary highlight is often visible with rough surfaces. Barla et al. ([^7]) described a model for such surfaces with a focus on perceptually meaningful parameters for it.

Weyrich et al. ([^102]) have developed methods to infer a microfacet distribution that matches a measured or desired reflection distribution. Remarkably, they showed that it is possible to manufacture actual physical surfaces that match a desired reflection distribution fairly accurately.

#### Layered Materials

Many materials are naturally composed of multiple layers—for example, a metal base surface tarnished with patina, or wood with a varnish coating. Using a specialized BRDF to represent such structures can be vastly more efficient than resolving internal reflections using standard light transport methods.

Hanrahan and Krueger ([^41]) modeled the layers of skin, accounting for just a single scattering event in each layer, and Dorsey and Hanrahan ([^30]) rendered layered materials using the Kubelka–Munk theory, which accounts for multiple scattering within layers but assumes that radiance distribution does not vary as a function of direction.

Pharr and Hanrahan ([^69]) showed that Monte Carlo integration could be used to solve the *adding equations* to efficiently compute BSDFs for layered materials without needing either of these simplifications. The adding equations are integral equations that accurately describe the effect of multiple scattering in layered media; they were derived by van de Hulst ([^93]) and Twomey et al. ([^91]).

Weidlich and Wilkie ([^98]) rendered layered materials more efficiently by making a number of simplifying assumptions. Guo et al. ([^39]) showed that both unidirectional and bidirectional Monte Carlo random walks through layers led to efficient algorithms for evaluation, sampling, and PDF evaluation. (Their unidirectional approach is implemented in pbrt ’s [LayeredBxDF](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Scattering_from_Layered_Materials.html#LayeredBxDF) in Section [14.3](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Scattering_from_Layered_Materials.html#sec:scattering-layered).) Xia et al. ([2020a](#cite:Xia2020)) described an improved importance sampling for this approach and Gamboa et al. ([^34]) showed that bidirectional sampling was unnecessary and described a more efficient approach for multiple layers.

Another approach for layered materials is to represent the aggregate scattering behavior of a layered surface using a parametric representation. Examples include Jakob et al. ([2014a](#cite:Jakob2014a)) and Zeltner and Jakob ([^110]), who applied the adding equations to discretized scattering matrices describing volumetric layers and rough interfaces. Guo et al. ([^38]) modeled coated surfaces using a modified microfacet scattering model. Belcour ([^9]) characterized individual layers’ scattering statistically, computed aggregate scattering using the adding equations, and then mapped the result to sums of lobes based on the Trowbridge–Reitz microfacet distribution function. Weier and Belcour ([^99]) generalized this approach to handle anisotropic reflection from the layer interfaces and Randrianandrasana et al. ([^72]) further generalized the model to improve accuracy and ensure energy conservation.

It is possible to apply similar approaches to aggregate scattering at other scales. For example, Blumer et al. precomputed the effect of multiple scattering in complex geometry like trees and stored the result in a form that allows for efficient evaluation and sampling ([Blumer et al. 2016](#cite:Blumer2016)).

#### BSDF (Re-)Parameterization and Acquisition

Improvements in data-acquisition technology have led to increasing amounts of detailed real-world BRDF data, even including BRDFs that are spatially varying (sometimes called “bidirectional texture functions,” BTFs) (Dana et al. [^26]). See Müller et al. ([^59]) for a survey of work in BRDF measurement until the year 2005 and Guarnera et al. ([^37]) for a survey through the following decade.

Fitting measured BRDF data to parametric reflection models is a difficult problem. Rusinkiewicz ([^77]) made the influential observation that reparameterizing the measured data can make it substantially easier to compress or fit to models. The topic of BRDF parameterizations has also been investigated by Stark et al. ([^84]) and in Marscher’s Ph.D. dissertation ([^60]).

Building on Rusinkiewicz’s parameterization, Matusik et al. ([2003a](#cite:Matusik03a), [2003b](#cite:Matusik03b)) designed a BRDF representation and an efficient measurement device that repeatedly photographs a spherical sample to simultaneously acquire BRDF evaluations for many directions. They used this device to assemble a collection of isotropic material measurements that is now known as the MERL BRDF database. Baek et al. ([^3]) extended this approach with additional optics to capture polarimetric BRDFs, whose evaluation yields Mueller matrices that characterize how reflection changes the polarization state of light. Nielsen et al. ([^64]) analyzed the manifold of MERL BRDFs to show that as few as 10–20 carefully chosen measurements can produce high-quality BRDF approximations.

Dupuy et al. ([^32]) developed a simple iterative procedure for fitting standard microfacet distributions to measured BRDFs. Dupuy and Jakob ([^31]) generalized this procedure to arbitrary data-driven microfacet distributions and used the resulting approximation to perform a measurement in reparameterized coordinates, which is the approach underlying the [MeasuredBxDF](https://pbr-book.org/4ed/Reflection_Models/Measured_BSDFs.html#MeasuredBxDF). They then used a motorized goniophotometer to spectroscopically acquire a collection of isotropic and anisotropic material samples that can be loaded into pbrt.

While the high-dimensional nature of reflectance functions can pose a serious impediment in any acquisition procedure, the resulting data can often be approximated much more compactly. Bagher et al. ([^4]) decomposed the MERL database into a set of 1D factors requiring 3.2 KiB per material. Vávra and Filip ([^92]) showed how lower-dimensional slices can inform a sparse measurement procedure for anisotropic materials.

#### Hair, Fur, and Fibers

Kajiya and Kay ([^51]) were the first to develop a reflectance model for hair fibers, observing the characteristic behavior of the underlying cylindrical reflectance geometry. For example, a thin and ideally specular cylinder under parallel illumination will reflect light into a 1D cone of angles. Reflection from a rough cylinder tends to concentrate around the specular 1D cone and decay with increasing angular distance. Kajiya and Kay proposed a phenomenological model combining diffuse and specular terms sharing these properties. For related work, see also the paper by Banks ([^6]), which discusses basic shading models for 1D primitives like hair. Goldman ([^36]) developed a probabilistic shading model that models reflection from collections of short hairs. Ward et al.’s survey ([^97]) has extensive coverage of early research in modeling, animating, and rendering hair.

Marschner et al. ([^61]) investigated the processes underlying scattering from hair and performed a variety of measurements of scattering from actual hair. They introduced the longitudinal/azimuthal decomposition and the use of the modified index of refraction to hair rendering. They then developed a scattering model where the longitudinal component was derived by first considering perfect specular paths and then allowing roughness by centering a Gaussian around them, and their azimuthal model assumed perfect specular reflections. They showed that this model agreed reasonably well with their measurements. Hery and Ramamoorthi ([^46]) showed how to sample the first term of this model and Pekelis et al. ([^68]) developed a more efficient approach to sampling all of its terms.

Zinke and Weber ([^114]) formalized different ways of modeling scattering from hair and clarified the assumptions underlying each of them. Starting with the *bidirectional fiber scattering distribution function* (BFSDF), which describes reflected differential radiance at a point on a hair as a fraction of incident differential power at another, they showed how assuming homogeneous scattering properties and a far-away viewer and illumination made it possible to simplify the eight-dimensional BFSDF to a four-dimensional *bidirectional curve scattering distribution function* (BCSDF). (Our implementation of the [HairBxDF](https://pbr-book.org/4ed/Reflection_Models/Scattering_from_Hair.html#HairBxDF) has glossed over some of these subtleties and opted for the simplicity of considering the scattering model as a BSDF.)

Sadeghi et al. ([^78]) developed a hair scattering model with artist-friendly controls; Ou et al. ([^67]) showed how to sample from its distribution. Ogaki et al. ([^65]) created a tabularized model by explicitly modeling hair microgeometry and following random walks through it.

D’Eon et al. ([^23], [^24]) made a number of improvements to Marschner et al.’s model. They showed that their term was not actually energy conserving and derived a new one that was; this is the model from Equation ([9.49](https://pbr-book.org/4ed/Reflection_Models/Scattering_from_Hair.html#eq:hair-mp)) that our implementation uses. (See also d’Eon ([^21]) for a more numerically stable formulation of for low roughness, as well as Jakob ([^49]) for notes related to sampling their term in a numerically stable way.) They also introduced a Gaussian to the azimuthal term, allowing for varying azimuthal roughness. A 1D quadrature method was used to integrate the model across the width of the hair.

The RGB values used for the hair pigments in [HairBxDF::SigmaAFromConcentration()](https://pbr-book.org/4ed/Reflection_Models/Scattering_from_Hair.html#HairBxDF::SigmaAFromConcentration) were computed by d’Eon et al. ([^23]), based on a model by Donner and Jensen ([^29]). The function implemented in the [HairBxDF::SigmaAFromReflectance()](https://pbr-book.org/4ed/Reflection_Models/Scattering_from_Hair.html#HairBxDF::SigmaAFromReflectance) method is due to Chiang et al. ([2016a](#cite:Chiang2016)), who created a cube of hair and rendered it with a variety of absorption coefficients and roughnesses while it was illuminated with a uniform white dome. They then fit a function that mapped from the hair’s azimuthal roughness and average color at the center of the front face of the cube to an absorption coefficient.

D’Eon et al. ([^25]) performed extensive Monte Carlo simulations of scattering from dielectric cylinders with explicitly modeled scales and glossy scattering at the boundary based on a Beckmann microfacet distribution. They showed that separable models did not model all the observed effects and that in particular the specular term modeled by varies over the surface of the cylinder and also depends on. They developed a non-separable scattering model, where both and vary as a function of, and showed that it fit their simulations very accurately.

All the scattering models we have described so far have been BCSDFs—they represent the overall scattering across the entire width of the hair in a single model. Such “far field” models assume both that the viewer is far away and that incident illumination is uniform across the hair’s width. In practice, both of these assumptions are invalid if one is using path tracing to model multiple scattering inside hair. Two recent models have considered scattering at a single point along the hair’s width, making them more suitable for accurately modeling “near field” scattering.

Yan et al. ([^106]) generalized d’Eon et al.’s model to account for scattering in the medulla, modeling a scattering cylinder in the interior of fur, and validated their model with a variety of measurements of actual animal fur. Subsequent work developed an efficient model that allows both near- and far-field evaluation ([Yan et al. 2017a](#cite:Yan2017:near-far-field)).

Chiang et al. ([2016a](#cite:Chiang2016)) showed that eliminating the integral over width from d’Eon et al.’s model works well in practice and that the sampling rates necessary for path tracing also worked well to integrate scattering over the curve width, giving a much more efficient implementation. They also developed the perceptually uniform parameterization of and that we have implemented in the [HairBxDF](https://pbr-book.org/4ed/Reflection_Models/Scattering_from_Hair.html#HairBxDF) as well as the inverse mapping from reflectance to used in our [HairBxDF::SigmaAFromReflectance()](https://pbr-book.org/4ed/Reflection_Models/Scattering_from_Hair.html#HairBxDF::SigmaAFromReflectance) method.

Further recent advances in hair and fur rendering include work by Khungurn and Marschner ([^53]), who developed a scattering model from elliptical fibers and showed that modeling fibers as elliptical rather than cylindrical gives a closer match to measured data. Benamira and Pattanaik ([^11]) recently proposed a model that accounts for both elliptical fibers and diffraction effects, which are significant at the scale of human hair.

Modeling and rendering the individual fibers of fabric is closely related to doing so for hair and fur. Recent work includes Zhao et al. ([^112]), who fit a procedural yarn model to CT-scanned yarn, and Aliaga et al. ([^1]), who demonstrated the complexity of scattering from a variety of cloth fibers and developed tabularized scattering functions for them using a precomputed simulation.

#### Glints and Microstructure

The microfacet reflection models in this chapter are all based on the assumption that so many microfacets are visible in a pixel that they can be accurately described by their aggregate statistical behavior. This assumption is not true for many real-world surfaces, where a relatively small number of microfacets may be visible in each pixel; examples of such surfaces include glittery car paint and plastics. Additionally, many types of rough surfaces that aren’t considered glittery (e.g., bead-blasted plastic) are characterized by bright high-frequency glints under directionally peaked illumination (e.g., the sun).

A common characteristic of many glint-rendering techniques is that they replace point evaluations of reflectance functions with a directional and/or spatial average covering a small region (e.g., a ray differential). With such an approach, a single sample suffices to find all glints visible within one pixel, which dramatically accelerates the rendering process.

One approach to rendering glints was introduced by Jakob et al. ([2014b](#cite:Jakob2014b)), who developed a temporally consistent stochastic process that samples glint positions on the fly during evaluation of a spatio-directional average. Wang et al. ([^96]) showed that the performance of this method could be improved by a separable approximation of the spatial and directional dimensions. These stochastic methods are compact but also very limited in terms of the glint distributions that can be modeled.

In production rendering systems, fine surface details are often modeled using bump- or normal maps. Glinty surface appearance tends to result when such surfaces have high-resolution detail as well as a specular BRDF, and when they are furthermore subject to sharp (e.g., point or directional) illumination. At the same time, such configurations produce an extremely challenging Monte Carlo integration problem that has motivated numerous specialized methods for rendering normal-mapped specular surfaces.

Yan et al. ([^109]) proposed a method that organizes the normal maps into a 4D spatio-directional data structure that can be queried to find reflecting surface regions. Yan et al. ([^108]) drastically reduced the cost of reflectance queries by converting the normal map into a large superposition of 4D Gaussian functions termed a *position-normal distribution*. Though image fidelity is excellent, the overheads of these methods can be significant: slow rendering in the former case, and lengthy preprocessing and storage requirements in the second case. Zhu et al. ([^113]) addressed both of these issues via clustering and runtime synthesis of normal map detail. Wang et al. ([2020a](#cite:Wang2020:microstructure)) substantially reduced the storage requirements by using a semi-procedural model that matches the statistics of an input texture. Zeltner et al. ([^111]) proposed a Newton-like equation-solving iteration that stochastically finds glints within texels of a normal map. Atanasov et al. ([^2]) developed a multi-level data structure for finding glints around a given half vector.

Other work in this area includes Raymond et al. ([^73]), who developed methods for rendering scratched surfaces, and Kuznetsov et al., who trained generative adversarial networks to represent microgeometry ([Kuznetsov et al. 2019](#cite:Kuznetsov2019)). Chermain et al. ([^16]) incorporated the effect of multiple scattering among the microstructure facets in such models, and Chermain et al. ([^15]) proposed a visible normal sampling technique for glint NDF. Loubet et al. recently developed a technique for sampling specular paths that is applicable to rendering caustics as well as rendering glints ([^58]).

#### Wave Optics

Essentially all physically based renderers are based on laws that approximate wave-optical behavior geometrically. At a high level, these approximations are sound given the large scale of depicted objects compared to the wavelength of light. At the same time, wave-optical properties tend to make themselves noticeable whenever geometric features occur at scales resembling the wavelength of light, and such features may indeed be present even on objects that are themselves drastically larger.

For example, consider a thin film of oil on a puddle, a tiny scratch on an otherwise smooth metallic surface, or an object with micron-scale surface microstructure. These cases can feature striking structural coloration caused by the interference of light, which a purely geometric simulation would not be able to reproduce. It may be tempting to switch to a full wave-optical simulation of light in such cases, though this line of thought quickly runs into fundamental limits: for example, using the Finite Difference Time Domain (FDTD) method, the simulation domain would need to be discretized at resolutions of and simulated using sub-femtosecond timesteps. This can still work when studying local behavior at the micron scale, but it is practically infeasible for scenes measured in centimeters or even meters. These challenges have motivated numerous specialized methods that reintroduce such wave-optical effects within an otherwise geometric simulation.

Moravec ([^62]) was the first to apply a wave optics model to computer graphics. Other early work in this area includes Bahar and Chakrabarti ([^5]) and Stam ([^82]), who modeled diffraction effects from random and periodic structures. Cuypers et al. ([^20]) modeled multiple diffraction phenomena using signed BSDFs based on Wigner Distribution Functions.

Musbach et al. ([^63]) applied the FDTD to obtain a BRDF of the iridescent microstructure of a Morpho butterfly. Their paper provides extensive references to previous work on this topic. Dhillon et al. ([^27]) developed a model of diffraction from small-scale biological features such as are present in snake skin. Belcour and Barla ([^10]) modeled thin film iridescence on a rough microfacet surface and showed the importance of this effect for materials such as leather and how the resulting spectral variation can be efficiently calculated in an RGB-based simulation. Werner et al. ([^100]) developed a model for rendering surfaces with iridescent scratches. Yan et al. ([^107]) presented a surface microstructure model that integrates over a coherence area to produce iridescent glints, revealing substantial differences between geometric and wave-based modeling. Toisoul and Ghosh ([^87]) presented a method for capturing and reproducing the appearance of periodic grating-like structures. Xia et al. ([2020b](#cite:Xia2020:wave)) showed that diffraction and interference are meaningful at the scale of fibers and developed a wave optics-based model for scattering from fibers that they validated with measured data.

In contrast to the above applications that reproduce dramatic goniochromatic effects, several works have studied how wave-based modeling can improve modeling of common materials (e.g., rough plastic or conductive surfaces). Löw et al. ([^56]) proposed and compared geometric and wave-based BRDF models in fits to materials to the MERL database. Dong et al. ([^28]) measured the surface microstructure of metal samples using a profilometer and used it to construct geometric and wave-based models that they then compared to goniophotometric measurements. Holzschuch and Pacanowski ([^47]) integrated diffraction effects into a microfacet model and showed that this gives a closer fit to measured data.

#### Additional Topics

The Lambertian BRDF is an idealized model; as noted earlier, it does not match many real-world BRDFs precisely. Oren and Nayar ([^66]) proposed an improved model based on Lambertian microfacets that allowed the specification of surface roughness. d’Eon has recently ([^22]) proposed a model based on scattering Lambertian spheres that matches the appearance of many materials well.

A number of researchers have investigated BRDFs based on modeling the small-scale geometric features of a reflective surface. This work includes the computation of BRDFs from bump maps by Cabral, Max, and Springmeyer ([^13]); Fournier’s normal distribution functions ([Fournier 1992](#cite:Fournier92)); and a technique by Westin, Arvo, and Torrance ([^101]), who applied Monte Carlo ray tracing to statistically model reflection from microgeometry and represented the resulting BRDFs with spherical harmonics. Wu et al. ([^104]) developed a system that made it possible to model microgeometry and specify its underlying BRDF while interactively previewing the resulting macro-scale BRDF, and Falster et al. ([^33]) computed BSDFs of microgeometry, accounting for both multiple scattering and diffraction.

The effect of the polarization of light is not modeled in pbrt, although for some scenes it can be an important effect; see, for example, the paper by Tannenbaum, Tannenbaum, and Wozny ([^86]) for information about how to extend a renderer to account for this effect. Fluorescence, where light is reflected at different wavelengths than the incident illumination, is also not modeled by pbrt; see Glassner ([^35]) and Wilkie et al. ([^103]) for more information on this topic.

Modeling reflection from a variety of specific types of surfaces has received attention from researchers, leading to specialized reflection models. Examples include wood ([Marschner et al. 2005](#cite:Marschner05)), car paint ([Ergun et al. 2016](#cite:Ergun2016)), paper ([Papas et al. 2014](#cite:Papas2014)), and pearlescent materials ([Guillén et al. 2020](#cite:Guillen2020)). Cloth remains a particularly challenging material to render; see the recent survey by Castillo et al. ([^14]) for comprehensive coverage of work in this area.

Sampling BSDFs well is a key component of efficient image synthesis. Szécsi et al. ([^85]) evaluated different approaches for sampling BSDFs that are comprised of multiple lobes. It is often only possible to sample some factors of a BSDF (e.g., when sampling the Torrance–Sparrow BRDF using the distribution of visible microfacet normals); Herholz et al. fit parametric sampling distributions to BSDFs in an effort to sample them more effectively ([Herholz et al. 2018](#cite:Herholz2018)).

pbrt ’s test suite uses statistical hypothesis tests to verify the correctness of its BSDF sampling routines. The idea of verifying such graphics-related Monte Carlo sampling routines using statistical tests was introduced by Subr and Arvo ([2007a](#cite:Subr2007a)). The test variant that is used in pbrt was originally developed as part of the *Mitsuba* renderer by Jakob ([^48]).

### References

[^1]: Aliaga, C., C. Castillo, D. Gutiérrez, M. A. Otaduy, J. Lopez-Moreno, and A. Jarabo. 2017. An appearance model for textile fibers. *Computer Graphics Forum* *36*  (4), 35–45.

[^2]: Atanasov, A., A. Wilkie, V. Koylazov, and J. Křivánek. 2021. A multiscale microfacet model based on inverse bin mapping. *Computer Graphics Forum (Proceedings of Eurographics)* *40*  (2), 103–13.

[^3]: Baek, S.-H., T. Zeltner, H. J. Ku, I. Hwang, X. Tong, W. Jakob, and M. H. Kim. 2020. Image-based acquisition and modeling of polarimetric reflectance. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *39*  (4), 139:1–14.

[^4]: Bagher, M. M., J. M. Snyder, and D. Nowrouzezahrai. 2016. A non-parametric factor microfacet model for isotropic BRDFs. *ACM Transactions on Graphics* *35*  (5), 159:1–16.

[^5]: Bahar, E., and S. Chakrabarti. 1987. Full-wave theory applied to computer-aided graphics for 3D objects. *IEEE Computer Graphics and Applications* *7*  (7), 46–60.

[^6]: Banks, D. C. 1994. Illumination in diverse codimensions. In *Proceedings of SIGGRAPH ’94*, Computer Graphics Proceedings, Annual Conference Series, 327–34.

[^7]: Barla, P., R. Pacanowski, and P. Vangorp. 2018. A composite BRDF model for hazy gloss. *Computer Graphics Forum* *37*  (4), 55–66.

[^8]: Beckmann, P., and A. Spizzichino. 1963. *The Scattering of Electromagnetic Waves from Rough Surfaces*. New York: Pergamon.

[^9]: Belcour, L. 2018. Efficient rendering of layered materials using an atomic decomposition with statistical operators. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *37*  (4), 73:1–15.

[^10]: Belcour, L., and P. Barla. 2017. A practical extension to microfacet theory for the modeling of varying iridescence. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 65:1–14.

[^11]: Benamira, A., and S. Pattanaik. 2021. A combined scattering and diffraction model for elliptical hair rendering. *Computer Graphics Forum (Proceedings of EGSR 2021)* *40*  (4), 163–75.

[^12]: Blinn, J. F. 1977. Models of light reflection for computer synthesized pictures. *Computer Graphics (SIGGRAPH ’77 Proceedings)* *11*, 192–98.

[^13]: Cabral, B., N. Max, and R. Springmeyer. 1987. Bidirectional reflection functions from surface bump maps. *Computer Graphics (SIGGRAPH ’87 Proceedings)* *21*, 273–81.

[^14]: Castillo, C., J. López-Moreno, and C. Aliaga. 2019. Recent advances in fabric appearance reproduction. *Computers & Graphics* *84*, 103–21.

[^15]: Chermain, X., B. Sauvage, J.-M. Dischler, and C. Dachsbacher. 2021. Importance sampling of glittering BSDFs based on finite mixture distributions. *Proceedings of the Eurographics Symposium on Rendering*, 45–53.

[^16]: Chermain, X., F. Claux, and S. Mérillou. 2019. Glint rendering based on a multiple-scattering patch BRDF. *Computer Graphics Forum* *38*  (4), 27–37.

[^17]: Conty Estevez, A., and C. Kulla. 2017. Production friendly microfacet sheen BRDF. *SIGGRAPH 2017 Talks*

[^18]: Cook, R. L., and K. E. Torrance. 1981. A reflectance model for computer graphics. *Computer Graphics (SIGGRAPH ’81 Proceedings)* *15*, 307–16.

[^19]: Cook, R. L., and K. E. Torrance. 1982. A reflectance model for computer graphics. *ACM Transactions on Graphics* *1*  (1), 7–24.

[^20]: Cuypers, T., T. Haber, P. Bekaert, S. B. Oh, and R. Raskar. 2012. Reflectance model for diffraction. *ACM Transactions on Graphics* *31*  (5), 122:1–11.

[^21]: d’Eon, E. 2013. Notes on *An energy-conserving hair reflectance model*

[^22]: d’Eon, E. 2021. An analytic BRDF for materials with spherical Lambertian scatterers. *Computer Graphics Forum (Proceedings of EGSR)* *40*  (4), 153–61.

[^23]: d’Eon, E., G. Francois, M. Hill, J. Letteri, and J.-M. Aubry. 2011. An energy-conserving hair reflectance model. *Computer Graphics Forum* *30*  (4), 1181–87.

[^24]: d’Eon, E., S. Marschner, and J. Hanika. 2013. Importance sampling for physically-based hair fiber models. *SIGGRAPH Asia 2013 Technical Briefs*, 25:1–4.

[^25]: d’Eon, E., S. Marschner, and J. Hanika. 2014. A fiber scattering model with non-separable lobes—supplemental report. In *SIGGRAPH 2014 Talks*, 46:1.

[^26]: Dana, K. J., B. van Ginneken, S. K. Nayar, and J. J. Koenderink. 1999. Reflectance and texture of real-world surfaces. *ACM Transactions on Graphics* *18*  (1), 1–34.

[^27]: Dhillon, D. S., J. Teyssier, M. Single, I. Gaponenko, M. C. Milinkovitch, and M. Zwicker. 2014. Interactive diffraction from biological nanostructures. *Computer Graphics Forum* *33*  (8), 177–88.

[^28]: Dong, Z., B. Walter, S. Marschner, and D. P. Greenberg. 2015. Predicting appearance from measured microgeometry of metal surfaces. *ACM Transactions on Graphics* *35*  (1), 9:1–13.

[^29]: Donner, C., and H. W. Jensen. 2006. A spectral BSSRDF for shading human skin. *Rendering Techniques 2006: 17th Eurographics Workshop on Rendering*, 409–17.

[^30]: Dorsey, J., and P. Hanrahan. 1996. Modeling and rendering of metallic patinas. In *Proceedings of SIGGRAPH ’96*, 387–96.

[^31]: Dupuy, J., and W. Jakob. 2018. An adaptive parameterization for efficient material acquisition and rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *37*  (6), 274:1–14.

[^32]: Dupuy, J., E. Heitz, J.-C. Iehl, P. Poulin, and V. Ostromoukhov. 2015. Extracting microfacet-based BRDF parameters from arbitrary materials with power iterations. *Computer Graphics Forum (Proceedings of the 2015 Eurographics Symposium on Rendering)* *34*  (4), 21–30.

[^33]: Falster, V., A. Jarabo, and J. R. Frisvad. 2020. Computing the bidirectional scattering of a microstructure using scalar diffraction theory and path tracing. *Computer Graphics Forum* *39*  (7), 231–42.

[^34]: Gamboa, L. E., A. Gruson, and D. Nowrouzezahrai. 2020. An efficient transport estimator for complex layered materials. *Computer Graphics Forum* *39*  (2), 363–71.

[^35]: Glassner, A. 1994. A model for fluorescence and phosphorescence. *Proceedings of the Fifth Eurographics Workshop on Rendering*, 57–68.

[^36]: Goldman, D. B. 1997. Fake fur rendering. *Proceedings of SIGGRAPH ’97*, Computer Graphics Proceedings, Annual Conference Series, 127–34.

[^37]: Guarnera, D., G. Guarnera, A. Ghosh, C. Denk, and M. Glencross. 2016. BRDF representation and acquisition. *Computer Graphics Forum (Eurographics State of the Art Report)* *35*  (2), 625–50.

[^38]: Guo, J., J. Qian, Y. Guo. and J. Pan. 2017. Rendering thin transparent layers with extended normal distribution functions. *IEEE Transactions on Visualization & Computer Graphics* *23*  (9), 2108–19.

[^39]: Guo, Y., M. Hašan, and S. Zhao. 2018. Position-free Monte Carlo simulation for arbitrary layered BSDFs. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *37*  (6), 279:1–14.

[^40]: Hall, R. 1989. *Illumination and Color in Computer Generated Imagery*. New York: Springer-Verlag.

[^41]: Hanrahan, P., and W. Krueger. 1993. Reflection from layered surfaces due to subsurface scattering. *Computer Graphics (SIGGRAPH ’93 Proceedings)*, 165–74.

[^42]: Heitz, E. 2014. Understanding the masking-shadowing function in microfacet-based BRDFs. *Journal of Computer Graphics Techniques (JCGT)* *3*  (2), 32–91.

[^43]: Heitz, E. 2015. Derivation of the microfacet function. Personal communication.

[^44]: Heitz, E. 2018. Sampling the GGX distribution of visible normals. *Journal of Computer Graphics Techniques (JCGT)* *7*  (4), 1–13.

[^45]: Heitz, E., and E. d’Eon. 2014. Importance sampling microfacet-based BSDFs using the distribution of visible normals. *Computer Graphics Forum (Proceedings of the 2014 Eurographics Symposium on Rendering)* *33*  (4), 103–12.

[^46]: Hery, C., and R. Ramamoorthi. 2012. Importance sampling of reflection from hair fibers. *Journal of Computer Graphics Techniques (JCGT)* *1*  (1), 1–17.

[^47]: Holzschuch, N., and R. Pacanowski. 2017. A two-scale microfacet reflectance model combining reflection and diffraction. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 66:1–12.

[^48]: Jakob, W. 2010. Mitsuba renderer. [http://www.mitsuba-renderer.org](http://www.mitsuba-renderer.org/)

[^49]: Jakob, W. 2012. Numerically stable sampling of the von Mises Fisher distribution on (and other tricks). [https://www.mitsuba-renderer.org/ wenzel/files/vmf.pdf](https://www.mitsuba-renderer.org/%C2%A0wenzel/files/vmf.pdf)

[^50]: Kajiya, J. T. 1985. Anisotropic reflection models. *Computer Graphics (Proceedings of SIGGRAPH ’85)* *19*, 15–21.

[^51]: Kajiya, J. T., and T. L. Kay. 1989. Rendering fur with three dimensional textures. *Computer Graphics (Proceedings of SIGGRAPH ’89)* *23*, 271–80.

[^52]: Kelemen, C., and L. Szirmay-Kalos. 2001. A microfacet based coupled specular-matte BRDF model with importance sampling. *Eurographics 2001—Short Presentations*

[^53]: Khungurn, P., and S. Marschner. 2017. Azimuthal scattering from elliptical hair fibers. *ACM Transactions on Graphics* *36*  (2), 13:1–23.

[^54]: Kulla, C., and A. Conty Estevez. 2017. Revisiting physically based shading at Imageworks. *SIGGRAPH 2017 Course Notes* *2*  (3).

[^55]: Kurt, M., L. Szirmay-Kalos, and J. Křivánek. 2010. An anisotropic BRDF model for fitting and Monte Carlo rendering. *SIGGRAPH Computer Graphics* *44*  (1), 3:1–15.

[^56]: Löw, J., J. Kronander, A. Ynnerman, and J. Unger. 2012. BRDF models for accurate and efficient rendering of glossy surfaces. *ACM Transactions on Graphics* *31*  (1), 9:1–14.

[^57]: Lee, J. H., A. Jarabo, D. S. Jeon, D. Gutierrez, and M. H. Kim. 2018. Practical multiple scattering for rough surfaces. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *37*  (6), 275:1–12.

[^58]: Loubet, G., T. Zeltner, N. Holzschuch, and W. Jakob. 2020. Slope-space integrals for specular next event estimation. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *39*  (6), 239:1–13.

[^59]: Müller, G., J. Meseth, M. Sattler, R. Sarlette, and R. Klein. 2005. Acquisition, synthesis and rendering of bidirectional texture functions. *Computer Graphics Forum (Eurographics State of the Art Report)* *24*  (1), 83–109.

[^60]: Marschner, S. 1998. Inverse rendering for computer graphics. Ph.D. thesis, Cornell University.

[^61]: Marschner, S. R., H. W. Jensen, M. Cammarano, S. Worley, and P. Hanrahan. 2003. Light scattering from human hair fibers. *ACM Transactions on Graphics* *22*  (3), 780–91.

[^62]: Moravec, H. 1981. 3D graphics and the wave theory. In *Computer Graphics*, Volume 15, 289–96.

[^63]: Musbach, A., G. W. Meyer, F. Reitich, and S. H. Oh. 2013. Full wave modelling of light propagation and reflection. *Computer Graphics Forum* *32*  (6), 24–37.

[^64]: Nielsen, J. B., H. W. Jensen, and R. Ramamoorthi. 2015. On optimal, minimal BRDF sampling for reflectance acquisition. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *34*  (6), 186:1–11.

[^65]: Ogaki, S., Y. Tokuyoshi, and S. Schoellhammer. 2010. An empirical fur shader. In *SIGGRAPH Asia 2010 Sketches*, 16:1–2.

[^66]: Oren, M., and S. K. Nayar. 1994. Generalization of Lambert’s reflectance model. In *Proceedings of SIGGRAPH ’94, Computer Graphics Proceedings, Annual Conference Series*, 239–46. New York: ACM Press.

[^67]: Ou, J., F. Xie, P. Krishnamachari, and F. Pellacini. 2012. ISHair: Importance sampling for hair scattering. *Computer Graphics Forum (Proceedings of the 2012 Eurographics Symposium on Rendering)* *31*  (4), 1537–45.

[^68]: Pekelis, L., C. Hery, R. Villemin, and J. Ling. 2015. A data-driven light scattering model for hair. *Pixar Technical Memo 15-02*

[^69]: Pharr, M., and P. M. Hanrahan. 2000. Monte Carlo evaluation of non-linear scattering equations for subsurface reflection. In *Proceedings of ACM SIGGRAPH 2000*, Computer Graphics Proceedings, Annual Conference Series, 75–84.

[^70]: Phong, B.-T. 1975. Illumination for computer generated pictures. *Communications of the ACM* *18*  (6), 311–17.

[^71]: Poulin, P., and A. Fournier. 1990. A model for anisotropic reflection. In *Computer Graphics (Proceedings of SIGGRAPH ’90)*, Volume 24, 273–82.

[^72]: Randrianandrasana, J., P. Callet, and L. Lucas. 2021. Transfer matrix based layered materials rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 177:1–16.

[^73]: Raymond, B., G. Guennebaud, and P. Barla. 2016. Multi-scale rendering of scratched materials using a structured SVBRDF model. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *35*  (4), 57:1–11.

[^74]: Ribardière, M., B. Bringier, D. Meneveaux, and L. Simonot. 2017. STD: Student’s t-distribution of slopes for microfacet based BSDFs. *Computer Graphics Forum* *36*  (2), 421–29.

[^75]: Ribardière, M., B. Bringier, L. Simonot, and D. Meneveaux. 2019. Microfacet BSDFs generated from NDFs and explicit microgeometry. *ACM Transactions on Graphics* *38*  (5), 143:1–15.

[^76]: Ross, V., D. Dion, and G. Potvin. 2005. Detailed analytical approach to the Gaussian surface bidirectional reflectance distribution function specular component applied to the sea surface. *Journal of the Optical Society of America* *22*  (11), 2442–53.

[^77]: Rusinkiewicz, S. 1998. A new change of variables for efficient BRDF representation. In *Proceedings of the Eurographics Rendering Workshop*, 11–23.

[^78]: Sadeghi, I., H. Pritchett, H. W. Jensen, and R. Tamstorf. 2010. An artist friendly hair shading system. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2010)* *29*  (4), 56:1–10.

[^79]: Simonot, L. 2009. Photometric model of diffuse surfaces described as a distribution of interfaced Lambertian facets. *Applied Optics* *48*  (30), 5793–801.

[^80]: Smith, B. 1967. Geometrical shadowing of a random rough surface. *IEEE Transactions on Antennas and Propagation* *15*  (5), 668–71.

[^81]: Sommerfeld, A., and J. Runge. 1911. Anwendungen der vektorrechnung auf die grundlagen der geometrischen optik. *Annalen der Physik* *340*  (7), 277–98.

[^82]: Stam, J. 1999. Diffraction shaders. In *Proceedings of SIGGRAPH ’99*, Computer Graphics Proceedings, Annual Conference Series, 101–10.

[^83]: Stam, J. 2001. An illumination model for a skin layer bounded by rough surfaces. In *Rendering Techniques 2001: 12th Eurographics Workshop on Rendering*, 39–52.

[^84]: Stark, M., J. Arvo, and B. Smits. 2005. Barycentric parameterizations for isotropic BRDFs. *IEEE Transactions on Visualization and Computer Graphics* *11*  (2), 126–38.

[^85]: Szécsi, L., L. Szirmay-Kalos, and C. Kelemen. 2003. Variance reduction for Russian roulette. *Journal of the World Society for Computer Graphics (WSCG)* *11* (1).

[^86]: Tannenbaum, D. C., P. Tannenbaum, and M. J. Wozny. 1994. Polarization and birefringency considerations in rendering. In *Proceedings of SIGGRAPH ’94*, Computer Graphics Proceedings, Annual Conference Series, 221–22.

[^87]: Toisoul, T., and A. Ghosh. 2017. Practical acquisition and rendering of diffraction effects in surface reflectance. *ACM Transactions on Graphics* *36*  (5), 166:1–16.

[^88]: Torrance, K. E., and E. M. Sparrow. 1967. Theory for off-specular reflection from roughened surfaces. *Journal of the Optical Society of America* *57*  (9), 1105–14.

[^89]: Trowbridge, S., and K. P. Reitz. 1975. Average irregularity representation of a rough ray reflection. *Journal of the Optical Society of America* *65*  (5), 531–36.

[^90]: Turquin, E., 2019. Practical multiple scattering compensation for microfacet models. *Industrial Light & Magic Technical Report*

[^91]: Twomey, S., H. Jacobowitz, and H. B. Howell. 1966. Matrix methods for multiple-scattering problems. *Journal of the Atmospheric Sciences* *32*, 289–96.

[^92]: Vávra, R., and J. Filip. 2016. Minimal sampling for effective acquisition of anisotropic BRDFs. *Computer Graphics Forum* *35*  (7), 299–309.

[^93]: van de Hulst, H. C. 1980. *Multiple Light Scattering*. New York: Academic Press.

[^94]: Walter, B., S. Marschner, H. Li, and K. Torrance. 2007. Microfacet models for refraction through rough surfaces. In *Rendering Techniques 2007 (Proc. Eurographics Symposium on Rendering)*, 195–206.

[^95]: Walter, B., Z. Dong, S. Marschner, and D. Greenberg. 2015. The ellipsoid normal distribution function. *Supplemental material of* Predicting Appearance from Measured Microgeometry of Metal Surfaces, *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *35*  (4), 9:1–13.

[^96]: Wang, B., L. Wang, and N. Holzschuch. 2018. Fast global illumination with discrete stochastic microfacets using a filterable model. *Computer Graphics Forum* *37*  (7), 55–64.

[^97]: Ward, K., F. Bertails, T.-Y. Kim, S. R. Marschner, M.-P. Cani, and M. Lin. 2007. A survey on hair modeling: Styling, simulation, and rendering. *IEEE Transactions on Visualization and Computer Graphics* *13*  (2), 213–34.

[^98]: Weidlich, A., and A. Wilkie. 2007. Arbitrarily layered micro-facet surfaces. In *Proceedings of the 5th International Conference on Computer Graphics and Interactive Techniques in Australia and Southeast Asia (GRAPHITE ’07)*, 171–78.

[^99]: Weier, P., and L. Belcour. 2020. Rendering layered materials with anisotropic interfaces. *Journal of Computer Graphics Techniques (JCGT)* *9*  (2), 37–57.

[^100]: Werner, S., Z. Velinov, W. Jakob, and M. Hullin. 2017. Scratch iridescence: Wave-optical rendering of diffractive surface structure. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *36*  (6), 207:1–14.

[^101]: Westin, S., J. Arvo, and K. Torrance. 1992. Predicting reflectance functions from complex surfaces. *Computer Graphics* *26*  (2), 255–64.

[^102]: Weyrich, T., P. Peers, W. Matusik, and S. Rusinkiewicz. 2009. Fabricating microgeometry for custom surface reflectance. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2008)* *28*  (3), 32:1–6.

[^103]: Wilkie, A., A. Weidlich, C. Larboulette, and W. Purgathofer. 2006. A reflectance model for diffuse fluorescent surfaces. In *Proceedings of GRAPHITE*, 321–31.

[^104]: Wu, H., J. Dorsey, and H. Rushmeier. 2011. Physically-based interactive bi-scale material design. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2011)* *30*  (6), 145:1–10.

[^105]: Xie, F., and P. Hanrahan. 2018. Multiple scattering from distributions of specular v-grooves. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *37*  (6), 276:1–14.

[^106]: Yan, L.-Q., C.-W. Tseng, H. W. Jensen, and R. Ramamoorthi. 2015. Physically-accurate fur reflectance: Modeling, measurement, and rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2015)* *34*  (6), 185:1–13.

[^107]: Yan, L.-Q., M. Hašan, B. Walter, S. Marschner, and R. Ramamoorthi. 2018. Rendering specular microgeometry with wave optics. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *37*  (4), 75:1–10.

[^108]: Yan, L.-Q., M. Hašan, S. Marschner, and R. Ramamoorthi. 2016. Position-normal distributions for efficient rendering of specular microstructure. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *35*  (4), 56:1–9.

[^109]: Yan, L.-Q., M. Hašan, W. Jakob, J. Lawrence, S. Marschner, and R. Ramamoorthi. 2014. Rendering glints on high-resolution normal-mapped specular surfaces. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2014)* *33*  (4), 116:1–9.

[^110]: Zeltner, T., and W. Jakob. 2018. The layer laboratory: A calculus for additive and subtractive composition of anisotropic surface reflectance. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *37*  (4), 74:1–14.

[^111]: Zeltner, T., I. Georgiev, and W. Jakob. 2020. Specular manifold sampling for rendering high-frequency caustics and glints. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *39*  (4), 149:1–15.

[^112]: Zhao, S., F. Luan, and K. Bala. 2016. Fitting procedural yarn models for realistic cloth rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *35*  (4), 51:1–11.

[^113]: Zhu, J., Y. Xu, and L. Wang. 2019. A stationary SVBRDF material modeling method based on discrete microsurface. *Computer Graphics Forum* *38*  (7), 745–54.

[^114]: Zinke, A., and A. Weber. 2007. Light scattering from filaments. *IEEE Transactions on Visualization and Computer Graphics* *13*  (2), 342–56.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[反射模型]]
- [[BSDF]]

**同章节**:
- [[9 Reflection Models]]
- [[9.1 BSDF Representation]]
- [[9.2 Diffuse Reflection]]
- [[9.3 Specular Reflection and Transmission]]
- [[9.4 Conductor BRDF]]
- [[9.5 Dielectric BSDF]]
- [[9.6 Roughness Using Microfacet Theory]]
- [[9.7 Rough Dielectric BSDF]]
- [[9.8 Measured BSDFs]]
- [[9.9 Scattering from Hair]]
