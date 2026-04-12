---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
Lommel ([^50]) first derived the equation of transfer. Not only did he derive this equation, but he also solved it in some simplified cases in order to estimate reflection functions from real-world surfaces (including marble and paper), and he compared his solutions to measured reflectance data from these surfaces. The equation of transfer was independently found by Khvolson ([^41]) soon afterward; see Mishchenko ([^58]) for a history of early work in the area.

Seemingly unaware of Lommel’s work, Schuster ([^75]) was the next researcher in radiative transfer to consider the effect of multiple scattering. He used the term *self-illumination* to describe the fact that each part of the medium is illuminated by every other part of the medium, and he derived differential equations that described reflection from a slab along the normal direction, assuming the presence of isotropic scattering. The conceptual framework that he developed remains essentially unchanged in the field of radiative transfer.

Soon thereafter, Schwarzschild ([^76]) introduced the concept of radiative equilibrium, and Jackson ([^31]) expressed Schuster’s equation in integral form, also noting that “the obvious physical mode of solution is Liouville’s method of successive substitutions” (i.e., a Neumann series solution). Finally, King ([^42]) completed the rediscovery of the equation of transfer by expressing it in the general integral form.

Books by Chandrasekhar ([^4]), Preisendorfer ([^71], [^72]), and van de Hulst ([^83]) cover volume light transport in depth. D’Eon’s book ([^5]) extensively discusses scattering problems, including both analytic and Monte Carlo solutions, and contains many references to related work in other fields.

The equation of transfer was introduced to graphics by Kajiya and Von Herzen ([^38]). Arvo ([^2]) made essential connections between previous formalizations of light transport in graphics and the equation of transfer as well as to the field of radiative transfer in general. Pauly et al. ([^63]) derived the generalization of the path integral form of the light transport equation for the volume-scattering case; see also Chapter 3 of Jakob’s Ph.D. thesis ([^32]) for a full derivation.

The integral null-scattering volume light transport equation was derived by Galtier et al. ([^15]) in the field of radiative transfer; Eymet et al. ([^13]) described the generalization to include scattering from surfaces. This approach was introduced to graphics by Novák et al. ([^62]). Miller et al. ([^57]) derived its path integral form, which made it possible to apply powerful variance reduction techniques based on multiple importance sampling.

#### Volumetric Path Tracing

von Neumann’s original description of the Monte Carlo algorithm was in the context of neutron transport problems ([Ulam et al. 1947](#cite:Ulam1947)); his technique included the algorithm for sampling distances from an exponential distribution (our Equation ([A.17](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_1D_Functions.html#eq:exponential-sampling-distance))), uniformly sampling 3D directions via uniform sampling of (as implemented in [SampleUniformSphere()](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_Multidimensional_Functions.html#SampleUniformSphere)), and randomly choosing among scattering events as described in Section [14.1.2](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/The_Equation_of_Transfer.html#sec:volumetric-path-tracing-deriv).

Rushmeier ([^73]) was the first to use Monte Carlo to solve the volumetric light transport equation in a general setting.

Szirmay-Kalos et al. ([^82]) precomputed interactions between sample points in the medium in order to more quickly compute multiple scattering. Kulla and Fajardo ([^46]) proposed a specialized sampling technique that is effective for light sources inside participating media. (This technique was first introduced in the field of neutron transport by Kalli and Cashwell ([^39]).) Georgiev et al. ([^16]) made the observation that incremental path sampling can generate particularly bad paths in participating media. They proposed new multi-vertex sampling methods that better account for all the relevant terms in the equation of transfer.

Sampling direct illumination from lights at points inside media surrounded by an interface is challenging; traditional direct lighting algorithms are not applicable at points inside the medium, as refraction through the interface will divert the shadow ray’s path. Walter et al. ([^86]) considered this problem and developed algorithms to efficiently find paths to lights accounting for this refraction. More recent work on this topic was done by Holzschuch ([^29]) and Koerner et al. ([^45]). Weber et al. ([^87]) developed an approach for more effectively sampling direct lighting in forward scattering media by allowing multiple scattering events along the path to the light.

Szirmay-Kalos et al. ([^80]) first showed the use of the integral null-scattering volume light transport equation for rendering scattering inhomogeneous media. Kutz et al. ([^47]) subsequently applied it to efficient rendering of spectral media and Szirmay-Kalos et al. ([^81]) developed improved algorithms for sampling multiple scattering. After deriving the path integral formulation, Miller et al. ([^57]) used it to show the effectiveness of combining a variety of sampling techniques using multiple importance scattering, including bidirectional path tracing.

The visual appearance of high-albedo objects like clouds is striking, but many bounces may be necessary for good results. Wrenninge et al. ([^90]) described an approximation where after the first few bounces, the scattering coefficient, the attenuation coefficient for shadow rays, and the eccentricity of the phase function are all progressively reduced. Kallweit et al. ([^40]) applied neural networks to store precomputed multiple scattering solutions for use in rendering highly scattering clouds.

Pegoraro et al. ([2008b](#cite:Pegoraro08)) developed a Monte Carlo sampling approach for rendering participating media that used information from previous samples to guide future sampling. More recent work in volumetric path guiding by Herholz et al. applied product sampling based on the phase function and an approximation to the light distribution in the medium ([Herholz et al. 2019](#cite:Herholz2019)). Wrenninge and Villemin ([^89]) developed a volumetric product sampling approach based on adapting the majorant to account for important regions of the integrand and then randomly selecting among candidate samples based on weights that account for factors beyond transmittance. Villeneuve et al. ([^85]) have also developed algorithms for product sampling in media, accounting for the surface normal at area light sources, transmittance along the ray, and the phase function.

Volumetric emission is not handled efficiently by the [VolPathIntegrator](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Volume_Scattering_Integrators.html#VolPathIntegrator), as there is no specialized sampling technique to account for it. Villemin and Hery ([^84]) precomputed tabularized CDFs for sampling volumetric emission, and Simon et al. ([^77]) developed further improvements, including integrating emission along rays and using the sampled point in the volume solely to determine the initial sampling direction, which gives better results in dense media.

The one-dimensional volumetric light transport algorithms implemented in [LayeredBxDF](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Scattering_from_Layered_Materials.html#LayeredBxDF) are based on Guo et al.’s approach ([^21]).

#### Other Light Transport Algorithms

Blinn ([1982b](#cite:Blinn82)) first used basic volume scattering algorithms for computer graphics. Rushmeier and Torrance ([^74]) used finite-element methods for rendering participating media. Other early work in volume scattering for computer graphics includes work by Max ([^55]); Nishita, Miyawaki, and Nakamae ([^61]); Bhate and Tokuta’s approach based on spherical harmonics ([Bhate and Tokuta 1992](#cite:Bhate92)), and Blasi et al.’s two-pass Monte Carlo algorithm, where the first pass shoots energy from the lights and stores it in a grid and the second pass does final rendering using the grid to estimate illumination at points in the scene ([Blasi et al. 1993](#cite:Blasi93)). Glassner ([^19]) provided a thorough overview of this topic and early applications of it in graphics, and Max’s survey article (Max [^54]) also covers early work well. See Cerezo et al. ([^3]) for an extensive survey of approaches to rendering participating media up through 2005.

One important application of volume scattering algorithms in computer graphics has been simulating atmospheric scattering. Work in this area includes early papers by Klassen ([^43]) and Preetham et al. ([^70]), who introduced a physically rigorous and computationally efficient atmospheric and sky-lighting model. Haber et al. ([^23]) described a model for twilight, and Hošek and Wilkie ([^27], [^28]) developed a comprehensive model for sky- and sunlight. Bruneton evaluated the accuracy and efficiency of a number of models for atmospheric scattering ([Bruneton 2017](#cite:Bruneton2017)). A sophisticated model that accurately accounts for polarization, observers at arbitrary altitudes, and the effect of atmospheric scattering for objects at finite distances was recently introduced by Wilkie et al. ([^88]).

Jarosz et al. ([2008a](#cite:Jarosz:radiancecache)) first extended the principles of irradiance caching to participating media. Marco et al. ([^53]) described a state-of-the-art algorithm for volumetric radiance caching based on Schwarzhaupt et al.’s surface-based second-order derivatives ([Schwarzhaupt et al. 2012](#cite:Schwarzhaupt12)).

Jensen and Christensen ([^37]) were the first to generalize the photon-mapping algorithm to participating media. Knaus and Zwicker ([^44]) showed how to render participating media using stochastic progressive photon mapping (SPPM). Jarosz et al. ([2008b](#cite:Jarosz08)) had the important insight that expressing the scattering integral over a beam through the medium as the measurement to be evaluated could make photon mapping’s rate of convergence much higher than if a series of point photon estimates was instead taken along each ray. Section 5.6 of Hachisuka’s thesis ([^24]) and Jarosz et al. ([2011a](#cite:Jarosz2011a), [2011b](#cite:Jarosz2011b)) showed how to apply this approach progressively. For another representation, see Jakob et al. ([^34]), who fit a sum of anisotropic Gaussians to the equilibrium radiance distribution in participating media.

Many of the other bidirectional light transport algorithms discussed in the “Further Reading” section of Chapter [13](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection.html#chap:light-transport) also have generalizations to account for participating media. See also Jarosz’s thesis ([^35]), which has extensive background on this topic and includes a number of important contributions.

Some researchers have had success in deriving closed-form expressions that describe scattering along unoccluded ray segments in participating media; these approaches can be substantially more efficient than integrating over a series of point samples. See Sun et al. ([^79]), Pegoraro and Parker ([^65]), and Pegoraro et al. ([^68], [^67], [^66]) for examples of such methods. (Remarkably, Pegoraro and collaborators’ work provides a closed-form expression for scattering from a point light source along a ray passing through homogeneous participating media with anisotropic phase functions.)

#### Subsurface Scattering

Subsurface scattering models based on volumetric light transport were first introduced to graphics by Hanrahan and Krueger ([^25]), although their approach did not attempt to simulate light that entered the object at points other than at the point being shaded. Dorsey et al. ([^12]) applied photon maps to simulating subsurface scattering that did include this effect, and Pharr and Hanrahan ([^69]) introduced an approach based on computing BSSRDFs for arbitrary scattering media with an integral over the medium’s depth.

The *diffusion approximation* has been shown to be an effective way to model highly scattering media for rendering. It was first introduced to graphics by Kajiya and Von Herzen ([^38]), though Stam ([^78]) was the first to clearly identify many of its advantages for rendering.

A solution of the diffusion approximation based on dipoles was developed by Farrell et al. ([^14]); that approach was applied to BSSRDF modeling for rendering by Jensen et al. ([2001b](#cite:Jensen01)). Subsequent work by Jensen and Buhler ([^36]) improved the efficiency of that method. A more accurate solution based on *photon beam diffusion* was developed by Habel et al. ([^22]). (The online edition of this book includes the implementation of a BSSRDF model based on photon beam diffusion as well as many more references to related work.)

Rendering realistic human skin is a challenging problem; this problem has driven the development of a number of new methods for rendering subsurface scattering after the initial dipole work as issues of modeling the layers of skin and computing more accurate simulations of scattering between layers have been addressed. For a good overview of these issues, see Igarashi et al.’s ([^30]) survey on the scattering mechanisms inside skin and approaches for measuring and rendering skin. Notable research in this area includes papers by Donner and Jensen ([^10]), d’Eon et al. ([^8]), Ghosh et al. ([^17]), and Donner et al. ([^11]). Donner’s thesis includes a discussion of the importance of accurate spectral representations for high-quality skin rendering (Donner [^9], Section 8.5). See Gitlina et al. ([^18]) for recent work in the measurement of the scattering properties of skin and fitting it to a BSSRDF model.

An alternative to BSSRDF-based approaches to subsurface scattering is to apply the same volumetric Monte Carlo path-tracing techniques that are used for other scattering media. This approach is increasingly used in production ([Chiang et al. 2016b](#cite:Chiang2016:subsurf)). See Wrenninge et al. ([^91]) for a discussion of such a model designed for artistic control and expressiveness.

Křivánek and d’Eon introduced the theory of zero-variance random walks for path-traced subsurface scattering, applying Dwivedi’s sampling technique ([1982a](#cite:Dwivedi1982a); [1982b](#cite:Dwivedi1982b)) to guide paths to stay close to the surface while maintaining an unbiased estimator ([Křivánek and d’Eon 2014](#cite:Krivanek2014:zerovar)). Meng et al. ([^56]) developed further improvements to this approach, including strategies that handle back-lit objects more effectively. More recent work on zero-variance theory by d’Eon and Křivánek ([^7]) includes improved results with isotropic scattering and new sampling schemes that further reduce variance.

Leonard et al. ([^49]) applied machine learning to subsurface scattering, training conditional variational auto-encoders to sample scattering, to model absorption probabilities, and to sample the positions of ray paths in spherical regions. They then used these capabilities to implement an efficient sphere-tracing algorithm.

#### Generalizations

Moon et al. ([^60]) made the important observation that some of the assumptions underlying the use of the equation of transfer—that the scattering particles in the medium are not too close together so that scattering events can be considered to be statistically independent—are not in fact true for interesting scenes that include small crystals, ice, or piles of many small glass objects. They developed a new light transport algorithm for these types of *discrete random media* based on composing precomputed scattering solutions. (See also concurrent work by Lee and O’Sullivan ([^48]) on composing scattering solutions.) Further work on rendering such materials was done by Müller et al. ([^52]), Guo et al. ([^20]), and Zhang and Zhao ([^92]).

*Non-exponential* media have distributions of interactions that are not described by an exponential distribution. They arise from media that have correlation in the distribution of their particles. The assumption of uncorrelated media that we adopted in Chapter [11](https://pbr-book.org/4ed/Volume_Scattering.html#chap:volume-scattering) and have used throughout this chapter can immediately be understood to be at minimum not quite right by considering the fact that there must be a minimum distance between any two particles; thus, the distribution cannot be perfectly uncorrelated. In practice, media with even more significant correlations are common; a variety of physical effects that lead to them are described by Bitterli et al. ([2018b](#cite:Bitterli2018)). Both d’Eon ([^6]) and Jarabo et al. ([2018a](#cite:Jarabo2018)) developed generalizations of the equation of transfer that allow non-exponential media. Bitterli et al. ([2018b](#cite:Bitterli2018)) presented a more general path integral form of it that maintains reciprocity and allows heterogeneous media.

Jakob et al. ([^33]) derived a generalized transfer equation that describes scattering by distributions of oriented particles. They proposed a *microflake* scattering model as a specific example of a particle distribution (where a microflake is the volumetric analog of a microfacet on a surface) and showed a number of ways of solving this equation based on Monte Carlo, finite elements, and a dipole model. More recently, Heitz et al. ([^26]) derived a generalized microflake distribution, which is considerably more efficient to sample and evaluate. Their model quantifies the local scattering properties using projected areas observed from different directions, which adds a well-defined notion of volumetric level of detail. Zhao et al. ([^93]) and Loubet and Neyret ([^51]) developed techniques for downsampling microflake distributions while still maintaining their visual appearance.

The equation of transfer assumes that the index of refraction of a medium will only change at discrete boundaries, though many actual media have continuously varying indices of refraction. Ament et al. ([^1]) derived a variant of the equation of transfer that allows for this case and applied photon mapping to render images with it. Pediredla et al. ([^64]) further investigated this topic and developed an unbiased rendering algorithm for such media based on path tracing.

Handling fluorescence in the context of volumetric scattering introduces a number of complexities discussed by Mojzík et al. ([^59]), who also derived a fluorescence-aware sampling algorithm.

### References

[^1]: Ament, M., C. Bergmann, and D. Weiskopf. 2014. Refractive radiative transfer equation. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2014)* *33*  (2), 17:1–22.

[^2]: Arvo, J. 1993. Transfer equations in global illumination. In *Global Illumination, SIGGRAPH ’93 Course Notes*, Volume 42, 1:1–30.

[^3]: Cerezo, E., F. Perez-Cazorla, X. Pueyo, F. Seron, and F. Sillion. 2005. A survey on participating media rendering techniques. *The Visual Computer* *21*  (5), 303–28.

[^4]: Chandrasekhar, S. 1960. *Radiative Transfer*. New York: Dover Publications. Originally published by Oxford University Press, 1950.

[^5]: d’Eon, E. 2016. *A Hitchhiker’s Guide to Multiple Scattering*. [http://www.eugenedeon.com/hitchhikers](http://www.eugenedeon.com/hitchhikers)

[^6]: d’Eon, E. 2018. A reciprocal formulation of non-exponential radiative transfer. 1: Sketch and motivation. arXiv:1803.03259 \[physics.comp-ph\].

[^7]: d’Eon, E., and J. Křivánek. 2020. Zero-variance theory for efficient subsurface scattering. *SIGGRAPH 2020 Course: Advances in Monte Carlo rendering: The legacy of Jaroslav Křivánek*, 3:1–366.

[^8]: d’Eon, E., D. Luebke, and E. Enderton. 2007. Efficient rendering of human skin. In *Rendering Techniques 2007: 18th Eurographics Workshop on Rendering*, 147–58.

[^9]: Donner, C. 2006. Towards realistic image synthesis of scattering materials. Ph.D. thesis, University of California, San Diego.

[^10]: Donner, C., and H. W. Jensen. 2006. A spectral BSSRDF for shading human skin. *Rendering Techniques 2006: 17th Eurographics Workshop on Rendering*, 409–17.

[^11]: Donner, C., T. Weyrich, E. d’Eon, R. Ramamoorthi, and S. Rusinkiewicz. 2008. A layered, heterogeneous reflectance model for acquiring and rendering human skin. *ACM Transactions on Graphics (Proceedings of ACM SIGGRAPH Asia 2008)* *27*  (5), 140:1–12.

[^12]: Dorsey, J., A. Edelman, J. Legakis, H. W. Jensen, and H. K. Pedersen. 1999. Modeling and rendering of weathered stone. In *Proceedings of SIGGRAPH ’99*, Computer Graphics Proceedings, Annual Conference Series, 225–34.

[^13]: Eymet, V., D. Poitou, M. Galtier, M. El-Hafi, G. Terrée, and R. Fournier. 2013. Null-collision meshless Monte-Carlo—Application to the validation of fast radiative transfer solvers embedded in combustion simulators. *Journal of Quantitative Spectroscopy and Radiative Transfer* *129*, 145–57.

[^14]: Farrell, T., M. Patterson, and B. Wilson. 1992. A diffusion theory model of spatially resolved, steady-state diffuse reflectance for the noninvasive determination of tissue optical properties *in vivo*. *Med. Phys.* *19*  (4), 879–88.

[^15]: Galtier, M., S. Blanco, C. Caliot, C. Coustet, J. Dauchet, M. El Hafi, V. Eymet, R. Fournier, J. Gautrais, A. Khuong, B. Piaud, and G. Terrée. 2013. Integral formulation of null-collision Monte Carlo algorithms. *Journal of Quantitative Spectroscopy and Radiative Transfer* *125*, 57–68.

[^16]: Georgiev, I., J. Křivánek, T. Hachisuka, D. Nowrouzezahrai, and W. Jarosz. 2013. Joint importance sampling of low-order volumetric scattering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2013)* *32*  (6), 164:1–14.

[^17]: Ghosh, A., T. Hawkins, P. Peers, S. Frederiksen, and P. Debevec. 2008. Practical modeling and acquisition of layered facial reflectance. *ACM Transactions on Graphics (Proceedings of ACM SIGGRAPH Asia 2008)* *27*  (5), 139:1–10.

[^18]: Gitlina, Y., G. C. Guarnera, D. D. Singh, J. Hansen, A. Lattas, D. Pai, and A. Ghosh. 2020. Practical measurement and reconstruction of spectral skin reflectance. *Computer Graphics Forum* *39*  (4), 75–89.

[^19]: Glassner, A. 1995. *Principles of Digital Image Synthesis*. San Francisco: Morgan Kaufmann.

[^20]: Guo, J., Y. Chen, B. Hu, L.-Q. Yan, Y. Guo, and Y. Liu. 2019. Fractional Gaussian fields for modeling and rendering of spatially-correlated media. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *38*  (4), 45:1–13.

[^21]: Guo, Y., M. Hašan, and S. Zhao. 2018. Position-free Monte Carlo simulation for arbitrary layered BSDFs. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *37*  (6), 279:1–14.

[^22]: Habel, R., P. H. Christensen, and W. Jarosz. 2013. Photon beam diffusion: A hybrid Monte Carlo method for subsurface scattering. *Computer Graphics Forum (Proceedings of the 2013 Eurographics Symposium on Rendering)* *32*  (4), 27–37.

[^23]: Haber, J., M. Magnor, and H.-P. Seidel. 2005. Physically-based simulation of twilight phenomena. *ACM Transactions on Graphics* *24*  (4), 1353–73.

[^24]: Hachisuka, T. 2011b. Robust light transport simulation using progressive density estimation. Ph.D. thesis, University of California, San Diego.

[^25]: Hanrahan, P., and W. Krueger. 1993. Reflection from layered surfaces due to subsurface scattering. *Computer Graphics (SIGGRAPH ’93 Proceedings)*, 165–74.

[^26]: Heitz, E., J. Dupuy, C. Crassin, and C. Dachsbacher. 2015. The SGGX microflake distribution. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2015)* *34*  (4), 48:1–11.

[^27]: Hošek, L., and A. Wilkie. 2012. An analytic model for full spectral sky-dome radiance. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2012)* *31*  (4), 95:1–9.

[^28]: Hošek, L., and A. Wilkie. 2013. Adding a solar-radiance function to the Hošek–Wilkie skylight model. *IEEE Computer Graphics and Applications* *33*  (3), 44–52.

[^29]: Holzschuch, N. 2015. Accurate computation of single scattering in participating media with refractive boundaries. *Computer Graphics Forum* *34*  (6), 48–59.

[^30]: Igarashi, T., K. Nishino, and S. K. Nayar. 2007. The appearance of human skin: A survey. *Foundations and Trends in Computer Graphics and Vision* *3*  (1), 1–95.

[^31]: Jackson, W. H. 1910. The solution of an integral equation occurring in the theory of radiation. *Bulletin of the American Mathematical Society* *16*, 473–75.

[^32]: Jakob, W. 2013. Light transport on path-space manifolds. Ph.D. thesis, Cornell University.

[^33]: Jakob, W., A. Arbree, J. T. Moon, K. Bala, and M. Steve. 2010. A radiative transfer framework for rendering materials with anisotropic structure. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2010)* *29*  (4), 53:1–13.

[^34]: Jakob, W., C. Regg, and W. Jarosz. 2011. Progressive expectation-maximization for hierarchical volumetric photon mapping. *Computer Graphics Forum (Proceedings of the 2011 Eurographics Symposium on Rendering)* *30*  (4), 1287–97.

[^35]: Jarosz, W. 2008. Efficient Monte Carlo methods for light transport in scattering media. Ph.D. thesis, UC San Diego.

[^36]: Jensen, H. W., and J. Buhler. 2002. A rapid hierarchical rendering technique for translucent materials. *ACM Transactions on Graphics* *21*  (3), 576–81.

[^37]: Jensen, H. W., and P. H. Christensen. 1998. Efficient simulation of light transport in scenes with participating media using photon maps. In *SIGGRAPH ’98 Conference Proceedings*, Annual Conference Series, 311–20.

[^38]: Kajiya, J. T., and B. P. Von Herzen. 1984. Ray tracing volume densities. In *Computer Graphics (Proceedings of SIGGRAPH ’84)*, Volume 18, 165–74.

[^39]: Kalli, H. J., and E. D. Cashwell. 1977. Evaluation of three Monte Carlo estimation schemes for flux at a point. *LA-6865-MS*, Los Alamos National Laboratory.

[^40]: Kallweit, S., T. Müller, B. McWilliams, M. Gross, and J. Novák. 2017. Deep scattering: Rendering atmospheric clouds with radiance-predicting neural networks. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *36*  (6), 231:1–11.

[^41]: Khvolson, O. D. 1890. Grundzüge einer matematischen Theorie der inneren Diffusion des Lichtes. *Izv. Peterburg. Academii Nauk* *33*, 221–65.

[^42]: King, L. V. 1913. On the scattering and absorption of light in gaseous media, with applications to the intensity of sky radiation. *Philosophical Transactions of the Royal Society of London. Series A. Mathematical and Physical Sciences* *212*, 375–433.

[^43]: Klassen, R. V. 1987. Modeling the effect of the atmosphere on light. *ACM Transactions on Graphics* *6*  (3), 215–37.

[^44]: Knaus, C., and M. Zwicker. 2011. Progressive photon mapping: A probabilistic approach. *ACM Transactions on Graphics* *30*  (3), 25:1–13.

[^45]: Koerner, D., J. Novák, P. Kutz, R. Habel, and W. Jarosz. 2016. Subdivision next-event estimation for path-traced subsurface scattering. *Eurographics Symposium on Rendering—Experimental Ideas and Implementations*, 91–96.

[^46]: Kulla, C., and M. Fajardo. 2012. Importance sampling techniques for path tracing in participating media. *Computer Graphics Forum (Proceedings of the 2012 Eurographics Symposium on Rendering)* *31*  (4), 1519–28.

[^47]: Kutz, P., R. Habel, Y. K. Li, and J. Novák. 2017. Spectral and decomposition tracking for rendering heterogeneous volumes. *ACM Transactions on Graphics* *36*  (4), 111:1–16.

[^48]: Lee, R., and C. O’Sullivan. 2007. Accelerated light propagation through participating media. *Proceedings of the Sixth Eurographics / IEEE VGTC Conference on Volume Graphics*, 17–23.

[^49]: Leonard, L., K. Höhlein, and R. Westermann. 2021. Learning multiple-scattering solutions for sphere-tracing of volumetric subsurface effects. *Computer Graphics Forum (Proceedings of Eurographics)* *40*  (2), 165–78.

[^50]: Lommel, E. 1889. Die Photometrie der diffusen Zurückwerfung. *Annalen der Physik* *36*, 473–502.

[^51]: Loubet, G., and F. Neyret. 2018. A new microflake model with microscopic self-shadowing for accurate volume downsampling. *Computer Graphics Forum* *37*  (2), 111–21.

[^52]: Müller, T., M. Papas, M. Gross, W. Jarosz, and J. Novák. 2016. Efficient rendering of heterogeneous polydisperse granular media. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *35*  (6), 168:1–14.

[^53]: Marco, J., A. Jarabo, W. Jarosz, and D. Gutierrez. 2018. Second-order occlusion-aware volumetric radiance caching. *ACM Transactions on Graphics* *37*  (2), 20:1–14.

[^54]: Max, N. L. 1995. Optical models for direct volume rendering. *IEEE Transactions on Visualization and Computer Graphics* *1*  (2), 99–108.

[^55]: Max, N. L. 1986. Atmospheric illumination and shadows. In *Computer Graphics (Proceedings of SIGGRAPH ’86)*, Volume 20, 117–24.

[^56]: Meng, J., J. Hanika, and C. Dachsbacher. 2016. Improving the Dwivedi sampling scheme. *Computer Graphics Forum* *35*  (4), 37–44.

[^57]: Miller, B., I. Georgiev, and W. Jarosz. 2019. A null-scattering path integral formulation of light transport. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *38*  (4), 1–13.

[^58]: Mishchenko, M. I. 2013. 125 years of radiative transfer: Enduring triumphs and persisting misconceptions. *AIP Conference Proceedings* *1531*  (11), 11–18.

[^59]: Mojzík, M., A. Fichet, and A. Wilkie. 2018. Handling fluorescence in a uni-directional spectral path tracer. *Computer Graphics Forum* *37*  (4), 77–94.

[^60]: Moon, J., B. Walter, and S. Marschner. 2007. Rendering discrete random media using precomputed scattering solutions. *Rendering Techniques 2007: 18th Eurographics Workshop on Rendering*, 231–42.

[^61]: Nishita, T., Y. Miyawaki, and E. Nakamae. 1987. A shading model for atmospheric scattering considering luminous intensity distribution of light sources. In *Computer Graphics (Proceedings of SIGGRAPH ’87)*, Volume 21, 303–10.

[^62]: Novák, J., A. Selle, and W. Jarosz. 2014. Residual ratio tracking for estimating attenuation in participating media. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2014)* *33*  (6), 179:1–11.

[^63]: Pauly, M., T. Kollig, and A. Keller. 2000. Metropolis light transport for participating media. In *Rendering Techniques 2000: 11th Eurographics Workshop on Rendering*, 11–22.

[^64]: Pediredla, A., Y. K. Chalmiani, M. G. Scopelliti, M. Chamanzar, S. Narasimhan, and I. Gkioulekas. 2020. Path tracing estimators for refractive radiative transfer. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *39*  (6), 241:1–15.

[^65]: Pegoraro, V., and S. Parker. 2009. An analytical solution to single scattering in homogeneous participating media. *Computer Graphics Forum (Proceedings of Eurographics 2009)* *28*  (2), 329–35.

[^66]: Pegoraro, V., M. Schott, and P. Slusallek. 2011. A mathematical framework for efficient closed-form single scattering. In *Proceedings of Graphics Interface 2011*, 151–58.

[^67]: Pegoraro, V., M. Schott, and S. G. Parker. 2010. A closed-form solution to single scattering for general phase functions and light distributions. *Computer Graphics Forum (Proceedings of the 2010 Eurographics Symposium on Rendering)* *29*  (4), 1365–74.

[^68]: Pegoraro, V., M. Schott, and S. Parker. 2009. An analytical approach to single scattering for anisotropic media and light distributions. In *Proceedings of Graphics Interface 2009*, 71–77.

[^69]: Pharr, M., and P. M. Hanrahan. 2000. Monte Carlo evaluation of non-linear scattering equations for subsurface reflection. In *Proceedings of ACM SIGGRAPH 2000*, Computer Graphics Proceedings, Annual Conference Series, 75–84.

[^70]: Preetham, A. J., P. S. Shirley, and B. E. Smits. 1999. A practical analytic model for daylight. In *Proceedings of SIGGRAPH ’99*, Computer Graphics Proceedings, Annual Conference Series, 91–100.

[^71]: Preisendorfer, R. W. 1965. *Radiative Transfer on Discrete Spaces*. Oxford: Pergamon Press.

[^72]: Preisendorfer, R. W. 1976. *Hydrologic Optics*. Honolulu, Hawaii: U.S. Department of Commerce, National Oceanic and Atmospheric Administration.

[^73]: Rushmeier, H. E. 1988. Realistic image synthesis for scenes with radiatively participating media. Ph.D. thesis, Cornell University.

[^74]: Rushmeier, H. E., and K. E. Torrance. 1987. The zonal method for calculating light intensities in the presence of a participating medium. In *Computer Graphics (Proceedings of SIGGRAPH ’87)*, Volume 21, 293–302.

[^75]: Schuster, A. 1905. Radiation through a foggy atmosphere. *Astrophysical Journal* *21*  (1), 1–22.

[^76]: Schwarzschild, K. 1906. On the equilibrium of the sun’s atmosphere (Nachrichten von der Koniglichen Gesellschaft der Wissenschaften zu Gottigen). *Göttinger Nachrichten* *195*, 41–53.

[^77]: Simon, F., J. Hanika, T. Zirr, and C. Dachsbacher. 2017. Line integration for rendering heterogeneous emissive volumes. *Computer Graphics Forum* *36*  (4), 101–10.

[^78]: Stam, J. 1995. Multiple scattering as a diffusion process. In *Rendering Techniques (Proceedings of the Eurographics Rendering Workshop)*, 41–50.

[^79]: Sun, B., R. Ramamoorthi, S. Narasimhan, and S. Nayar. 2005. A practical analytic single scattering model for real time rendering. *ACM Transactions on Graphics* *24*  (3), 1040–49.

[^80]: Szirmay-Kalos, L., I. Georgiev, M. Magdics, B. Molnár, and D. Légrády. 2017. Unbiased light transport estimators for inhomogeneous participating media. *Computer Graphics Forum* *36*  (2), 9–19.

[^81]: Szirmay-Kalos, L., M. Magdics, and M. Sbert. 2018. Multiple scattering in inhomogeneous participating media using Rao-Blackwellization and control variates. *Computer Graphics Forum* *37*  (2), 63–74.

[^82]: Szirmay-Kalos, L., M. Sbert, and T. Umenhoffer. 2005. Real-time multiple scattering in participating media with illumination networks. *Rendering Techniques 2005: 16th Eurographics Workshop on Rendering*, 277–82.

[^83]: van de Hulst, H. C. 1980. *Multiple Light Scattering*. New York: Academic Press.

[^84]: Villemin, R., and C. Hery. 2013. Practical illumination from flames. *Journal of Computer Graphics Techniques (JCGT)* *2*  (2), 142–55.

[^85]: Villeneuve, K., A. Gruson, I. Georgiev, and D. Nowrouzezahrai. 2021. Practical product sampling for single scattering in media. *Proceedings of the Eurographics Symposium on Rendering*, 55–60.

[^86]: Walter, B., S. Zhao, N. Holzschuch, and K. Bala. 2009. Single scattering in refractive media with triangle mesh boundaries. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2009)* *28*  (3), 92:1–8.

[^87]: Weber, P., J. Hanika, and C. Dachsbacher. 2017. Multiple vertex next event estimation for lighting in dense, forward-scattering media. *Computer Graphics Forum* *36*  (2), 21–30.

[^88]: Wilkie, A., P. Vevoda, T. Bashford-Rogers, L. Hošek, T. Iser, M. Kolářová, T. Rittig, and J. Křivánek. 2021. A fitted radiance and attenuation model for realistic atmospheres. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 135:1–14.

[^89]: Wrenninge, M., and R. Villemin. 2020. Product importance sampling of the volume rendering equation using virtual density segments. *Pixar Technical Memo #20-01*

[^90]: Wrenninge, M., C. Kulla, and V. Lundqvist. 2013. Oz: The great and volumetric. In *ACM SIGGRAPH 2013 Talks*, 46:1.

[^91]: Wrenninge, M., R. Villemin, and C. Hery. 2017. Path traced subsurface scattering using anisotropic phase functions and non-exponential free flights. *Pixar Technical Memo #17-07*

[^92]: Zhang, C., and S. Zhao. 2020. Multi-scale appearance modeling of granular materials with continuously varying grain properties. *Computer Graphics Forum* *39*  (4).

[^93]: Zhao, S., L. Wu, F. Durand, and R. Ramamoorthi. 2016. Downsampling scattering parameters for rendering anisotropic media. *ACM Transactions on Graphics* *35*  (6), 166:1–11.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[光传输方程]]
- [[体积散射]]
- [[路径追踪]]

**同章节**:
- [[14 Light Transport II Volume Rendering]]
- [[14.1 The Equation of Transfer]]
- [[14.2 Volume Scattering Integrators]]
- [[14.3 Scattering from Layered Materials]]
