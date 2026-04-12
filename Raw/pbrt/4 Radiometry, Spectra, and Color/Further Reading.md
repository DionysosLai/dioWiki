---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
McCluney’s book on radiometry is an excellent introduction to the topic ([^21]). Preisendorfer ([^32]) also covered radiometry in an accessible manner and delved into the relationship between radiometry and the physics of light. Nicodemus et al. ([^28]) carefully defined the BRDF, BSSRDF, and various quantities that can be derived from them.

Books by Moon and Spencer ([^24], [^25]) and Gershun ([^11]) are classic early introductions to radiometry. Lambert’s seminal early writings about photometry from the mid-18th century have been translated into English by DiLaura ([Lambert 1760](#cite:Lambert1760)).

Preisendorfer ([^32]) has connected radiative transfer theory to Maxwell’s classical equations describing electromagnetic fields, and further work was done in this area by Fante ([^8]). Going well beyond earlier work that represented radiance with Wigner distribution functions to model wave effects ([Oh 2010](#cite:Oh2010), [Cuypers et al. 2012](#cite:Cuypers2012)), Steinberg and Yan ([^38]) have recently introduced a comprehensive model of light transport based on a wave model, including a generalization of the light transport equation.

Correctly implementing radiometric computations can be tricky: one missed cosine factor and one is computing a completely different quantity than expected. Debugging these sorts of issues can be quite time-consuming. Ou and Pellacini ([^29]) showed how to use C++’s type system to associate units with each term of these sorts of computations so that, for example, trying to add a radiance value to another value that represents irradiance would trigger a compile time error.

The books by McCluney ([^21]) and Malacara ([^19]) discuss blackbody emitters and the standard illuminants in detail. The Standard Illuminants are defined in a CIE Technical Report ([^2]); Judd et al. ([^17]) developed the approach that was used to define the D Standard Illuminant.

Wilkie and Weidlich ([^44]) noted that common practice in rendering has been to use the blackbody distribution of Equation ([4.17](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Light_Emission.html#eq:plancks-law)) to model light emission for rendering, while Kirchhoff’s law, Equation ([4.18](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Light_Emission.html#eq:kirchoffs-law)), would be more accurate. They also pointed out that as objects become hot, their BRDFs often change, which makes Kirchhoff’s law more difficult to adopt, especially in that models that account for the effect of temperature variation on BRDFs generally are not available.

#### Spectral Representations

Meyer was one of the first researchers to closely investigate spectral representations in graphics ([Meyer and Greenberg 1980](#cite:Meyer80); Meyer et al. [^23]). Hall ([^13]) summarized the state of the art in spectral representations through 1989, and Glassner’s *Principles of Digital Image Synthesis* ([^12]) covers the topic through the mid-1990s. Survey articles by Hall ([^14]), Johnson and Fairchild ([^16]), and Devlin et al. ([^4]) are good resources on early work on this topic.

Borges ([^1]) analyzed the error introduced from the tristimulus representation when used for spectral computation. A variety of approaches based on representing spectra using basis functions have been developed, including Peercy ([^30]), who developed a technique based on choosing basis functions in a scene-dependent manner by considering the spectral distributions of the lights and reflecting objects in the scene. Rougeron and Péroche ([^36]) projected all spectra in the scene onto a hierarchical basis (the Haar wavelets), and showed that this adaptive representation can be used to stay within a desired error bound. Ward and Eydelberg-Vileshin ([^43]) developed a method for improving the spectral fidelity of regular RGB-only rendering systems by carefully adjusting the color values provided to the system before rendering.

Another approach to spectral representation was investigated by Sun et al. ([^39]), who partitioned spectral distributions into a smooth base distribution and a set of spikes. Each part was represented differently, using basis functions that worked well for each of these parts of the distribution. Drew and Finlayson ([^5]) applied a “sharp” basis, which is adaptive but has the property that computing the product of two functions in the basis does not require a full matrix multiplication as many other basis representations do.

Both Walter et al. ([^42]) and Morley et al. ([^26]) described light transport algorithms based on associating a single wavelength with each light path. Evans and McCool ([^7]) generalized these techniques with *stratified wavelength clusters*, which are effectively the approach implemented in [SampledSpectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#SampledSpectrum) and [SampledWavelengths](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#SampledWavelengths).

Radziszewski et al. ([^33]) noted that it is not necessary to terminate all secondary spectral wavelengths when effects like dispersion happen at non-specular interfaces; they showed that it is possible to compute all wavelengths’ contributions for a single path, weighting the results using multiple importance sampling. Wilkie et al. ([^45]) used equally spaced point samples in the wavelength domain and showed how this approach can also be used for photon mapping and rendering of participating media.

#### Color

For background information on properties of the human visual system, Wandell’s book on vision is an excellent starting point ([Wandell 1995](#cite:Wandell95)). Ferwerda ([^10]) presented an overview of the human visual system for applications in graphics, and Malacara ([^19]) gave a concise overview of color theory and basic properties of how the human visual system processes color. Ciechanowski ([^3]) presented an excellent interactive introduction to color spaces; his treatment has influenced our presentation of the XYZ color space and chromaticity.

A number of different approaches have been developed for mapping out-of-gamut colors to ones that can be displayed on a device with particular display primaries. This problem can manifest itself in a few ways: a color’s chromaticity may be outside of the displayed range, its chromaticity may be valid but it may be too bright for display, or both may be out of range.

For the issue of how to handle colors with undisplayable chromaticities, see Rougeron and Péroche’s survey article, which includes references to many approaches ([Rougeron and Péroche 1998](#cite:Rougeron98)). This topic was also covered by Hall ([^13]). Morovi’s book ([^27]) covers this topic, and a more recent survey has been written by Faridul et al. ([^9]).

While high dynamic range displays that can display a wide range of intensities are now starting to become available, most of them are still not able to reproduce the full range of brightness in rendered images. This problem can be addressed with *tone reproduction* algorithms that use models of human visual response to make the most of displays’ available dynamic ranges. This topic became an active area of research starting with the work of Tumblin and Rushmeier ([^41]). The survey article of Devlin et al. ([^4]) summarizes most of the work in this area through 2002, giving pointers to the original papers. See Reinhard et al.’s book ([^34]) on high dynamic range imaging, which includes comprehensive coverage of this topic through 2010. More recently, Reinhard et al. ([^35]) have developed tone reproduction algorithms that consider both accurate brightness and color reproduction together, also accounting for the display and viewing environment, and Eilertsen et al. ([^6]) surveyed algorithms for tone mapping of video.

#### From RGB to Spectra

Glassner ([1989b](#cite:Glassner89rgb)) did early work on converting RGB values to spectral distributions. Smits ([^37]) optimized discrete reflectance spectra to reproduce primaries (red, green, blue) and combinations of primaries (yellow, cyan, magenta, white) based on the observation that linear interpolation in such an extended space tends to produce smoother reflectance spectra. Mallett and Yuksel ([^20]) presented a surprising result showing that linear interpolation of three carefully chosen spectra can fully cover the sRGB gamut, albeit at some cost in terms of smoothness. Meng et al. ([^22]) optimized a highly smooth spectral interpolant based on a dense sampling of the space of chromaticities, enabling usage independent of any specific RGB gamut.

The method described in Section [4.6.6](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#sec:rgb-to-spectrum) was developed by Jakob and Hanika ([^15]). Several properties motivated its choice in pbrt: the spectral representation is based on a smooth function family with 3 parameters (i.e., the same dimension as an RGB). Conversion can then occur in two steps: a preprocessing step (e.g., per texel) replaces RGB values with polynomial coefficients, while the performance-critical evaluation at render time only requires a few floating-point instructions. Jung et al. ([^18]) extended this approach, using fluorescence to permit conversion of highly saturated RGB values that cannot be recreated using reflection alone.

Peters et al. ([^31]) proposed a powerful parameterization of smooth reflectance spectra in terms of Fourier coefficients. Instead of using them in a truncated Fourier series, which would suffer from ringing, they built on the theory of moments to reconstruct smooth and energy-conserving spectra.

The previous methods all incorporated smoothness as a central design constraint. While natural spectra indeed often tend to be smooth, maximally smooth spectra are not necessarily the most natural, especially when more information about the underlying type of material is available. Otsu et al. ([2018b](#cite:Otsu2018:rgb)) processed a large database of measured spectra, using principal component analysis to create a data-driven interpolant. Tódová et al. ([^40]) built on the moment-based method by Peters et al. ([^31]) to precompute an efficient spectral interpolant that is designed to reproduce user-specified spectra for certain RGB inputs.

### References

[^1]: Borges, C. 1991. Trichromatic approximation for computer graphics illumination models. *Computer Graphics (Proceedings of SIGGRAPH ’91)* *25*, 101–4.

[^2]: CIE Technical Report. 2004. Colorimetry. *Publication 15:2004 (3rd ed.)*, CIE Central Bureau, Vienna.

[^3]: Ciechanowski, B. 2019. Color spaces. [https://ciechanow.ski/color-spaces/](https://ciechanow.ski/color-spaces/)

[^4]: Devlin, K., A. Chalmers, A. Wilkie, and W. Purgathofer. 2002. Tone reproduction and physically based spectral rendering. *Proceedings of Eurographics 2002*, 101–23.

[^5]: Drew, M., and G. Finlayson. 2003. Multispectral rendering without spectra. *Journal of the Optical Society of America A* *20*  (7), 1181–93.

[^6]: Eilertsen, G., R. K. Mantiuk, and J. Unger. 2017. A comparative review of tone-mapping algorithms for high dynamic range video. *Computer Graphics Forum (Eurographics State of the Art Report)* *36*  (2), 565–92.

[^7]: Evans, G., and M. McCool. 1999. Stratified wavelength clusters for efficient spectral Monte Carlo rendering. *Proceedings of Graphics Interface 1999*, 42–49.

[^8]: Fante, R. L. 1981. Relationship between radiative-transport theory and Maxwell’s equations in dielectric media. *Journal of the Optical Society of America* *71*  (4), 460–68.

[^9]: Faridul, H. S., T. Pouli, C. Chamaret, J. Stauder, E. Reinhard, D. Kuzovkin, and A. Tremeau. 2016. Colour mapping: A review of recent methods, extensions and applications. *Computer Graphics Forum* *35*  (1), 59–88.

[^10]: Ferwerda, J. A. 2001. Elements of early vision for computer graphics. *IEEE Computer Graphics and Applications* *21*  (5), 22–33.

[^11]: Gershun, A. 1939. The light field. *Journal of Mathematics and Physics* *18*  (1-4), 51–151.

[^12]: Glassner, A. 1995. *Principles of Digital Image Synthesis*. San Francisco: Morgan Kaufmann.

[^13]: Hall, R. 1989. *Illumination and Color in Computer Generated Imagery*. New York: Springer-Verlag.

[^14]: Hall, R. 1999. Comparing spectral color computation methods. *IEEE Computer Graphics and Applications* *19*  (4), 36–46.

[^15]: Jakob, W., and J. Hanika. 2019. A low-dimensional function space for efficient spectral upsampling. *Computer Graphics Forum (Proceedings of Eurographics)*   *38*  (2), 147–55.

[^16]: Johnson, G. M., and M. D. Fairchild. 1999. Full spectral color calculations in realistic image synthesis. *IEEE Computer Graphics and Applications* *19*  (4), 47–53.

[^17]: Judd, D. B., D. L. MacAdam, and G. Wyszecki. 1964. Spectral distribution of typical daylight as a function of correlated color temperature. *Journal of the Optical Society of America* *54*  (8), 1031–40.

[^18]: Jung, A., A. Wilkie, J. Hanika, W. Jakob, and C. Dachsbacher. 2019. Wide gamut spectral upsampling with fluorescence. *Computer Graphics Forum* *38*  (4), 87–96.

[^19]: Malacara, D. 2002. *Color Vision and Colorimetry: Theory and Applications*. SPIE—The International Society for Optical Engineering. Bellingham, WA.

[^20]: Mallett, I., and C. Yuksel. 2019. Spectral primary decomposition for rendering with sRGB reflectance. *Eurographics Symposium on Rendering–DL-only and Industry Track*

[^21]: McCluney, W. R. 1994. *Introduction to Radiometry and Photometry*. Boston: Artech House.

[^22]: Meng, J., F. Simon, J. Hanika, and C. Dachsbacher. 2015. Physically meaningful rendering using tristimulus colours. *Computer Graphics Forum (Proceedings of the 2015 Eurographics Symposium on Rendering)* *34*  (4), 31–40.

[^23]: Meyer, G. W., H. E. Rushmeier, M. F. Cohen, D. P. Greenberg, and K. E. Torrance. 1986. An experimental evaluation of computer graphics imagery. *ACM Transactions on Graphics* *5*  (1), 30–50.

[^24]: Moon, P., and D. E. Spencer. 1936. *The Scientific Basis of Illuminating Engineering*. New York: McGraw-Hill.

[^25]: Moon, P., and D. E. Spencer. 1948. *Lighting Design*. Reading, Massachusetts: Addison-Wesley.

[^26]: Morley, R. K., S. Boulos, J. Johnson, D. Edwards, P. Shirley, M. Ashikhmin, and S. Premoze. 2006. Image synthesis using adjoint photons. In *Proceedings of Graphics Interface 2006*, 179–86.

[^27]: Morovi, J. 2008. *Color Gamut Mapping*. New York: John Wiley & Sons.

[^28]: Nicodemus, F., J. Richmond, J. Hsia, I. Ginsburg, and T. Limperis. 1977. *Geometrical Considerations and Nomenclature for Reflectance*. NBS Monograph 160, Washington, D.C.: National Bureau of Standards, U.S. Department of Commerce.

[^29]: Ou, J., and F. Pellacini. 2010. SafeGI: Type checking to improve correctness in rendering system implementation. *Computer Graphics Forum (Proceedings of the 2010 Eurographics Symposium on Rendering)* *29*  (4), 1267–77.

[^30]: Peercy, M. S. 1993. Linear color representations for full spectral rendering. *Computer Graphics (SIGGRAPH ’93 Proceedings)*, Volume 27, 191–98.

[^31]: Peters, C., S. Merzbach, J. Hanika, and C. Dachsbacher. 2019. Using moments to represent bounded signals for spectral rendering. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *38*  (4), 136:1–14.

[^32]: Preisendorfer, R. W. 1965. *Radiative Transfer on Discrete Spaces*. Oxford: Pergamon Press.

[^33]: Radziszewski, M., K. Boryczko, and W. Alda. 2009. An improved technique for full spectral rendering. *Journal of WSCG* *17*  (1-3), 9–16.

[^34]: Reinhard, E., G. Ward, P. Debevec, S. Pattanaik, W. Heidrich, and K. Myszkowski. 2010. *High Dynamic Range Imaging: Acquisition, Display, and Image-Based Lighting*. San Francisco: Morgan Kaufmann.

[^35]: Reinhard, E., T. Pouli, T. Kunkel, B. Long, A. Ballestad, and G. Damberg. 2012. Calibrated image appearance reproduction. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2012)* *31*  (6), 201:1–11.

[^36]: Rougeron, G., and B. Péroche. 1997. An adaptive representation of spectral data for reflectance computations. In *Eurographics Rendering Workshop 1997*, 126–38.

[^37]: Smits, B. 1999. An RGB-to-spectrum conversion for reflectances. *Journal of Graphics Tools* *4*  (4), 11–22.

[^38]: Steinberg, S., and L.-Q. Yan. 2021. A generic framework for physical light transport. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 139:1–20.

[^39]: Sun, Y., F. D. Fracchia, M. S. Drew, and T. W. Calvert. 2001. A spectrally based framework for realistic image synthesis. *The Visual Computer* *17*  (7), 429–44.

[^40]: Tódová, L., A. Wilkie, and L. Fascione. 2021. Moment-based constrained spectral uplifting. *Proceedings of the Eurographics Symposium on Rendering*, 215–24.

[^41]: Tumblin, J., and H. E. Rushmeier. 1993. Tone reproduction for realistic images. *IEEE Computer Graphics and Applications* *13*  (6), 42–48.

[^42]: Walter, B., P. M. Hubbard, P. Shirley, and D. F. Greenberg. 1997. Global illumination using local linear density estimation. *ACM Transactions on Graphics* *16*  (3), 217–59.

[^43]: Ward, G., and E. Eydelberg-Vileshin. 2002. Picture perfect RGB rendering using spectral prefiltering and sharp color primaries. In *Proceedings of 13th Eurographics Workshop on Rendering*, 117–24.

[^44]: Wilkie, A., and A. Weidlich. 2011. A physically plausible model for light emission from glowing solid objects. *Computer Graphics Forum (Proceedings of the 2011 Eurographics Symposium on Rendering)* *30*  (4), 1269–76.

[^45]: Wilkie, A., S. Nawaz, M. Droske, A. Weidlich, and J. Hanika. 2014. Hero wavelength spectral sampling. *Computer Graphics Forum (Proceedings of the 2014 Eurographics Symposium on Rendering)* *33*  (4), 123–31.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[辐射度量学]]
- [[光谱渲染]]

**同章节**:
- [[4 Radiometry, Spectra, and Color]]
- [[4.1 Radiometry]]
- [[4.2 Working with Radiometric Integrals]]
- [[4.3 Surface Reflection]]
- [[4.4 Light Emission]]
- [[4.5 Representing Spectral Distributions]]
- [[4.6 Color]]
