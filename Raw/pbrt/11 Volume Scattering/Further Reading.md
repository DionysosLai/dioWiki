---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Volume_Scattering/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
The books written by van de Hulst ([^33]) and Preisendorfer ([^28], [^29]) are excellent introductions to volume light transport. The seminal book by Chandrasekhar ([^2]) is another excellent resource, although it is mathematically challenging. d’Eon’s book ([^5]) has rigorous coverage of this topic and includes extensive references to work in the area. Novák et al.’s report ([^25]) provides a comprehensive overview of research in volumetric light transport for rendering through 2018; see also the “Further Reading” section of Chapter [14](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering.html#chap:volume-integration) for more references on this topic.

The Henyey–Greenstein phase function was originally described by Henyey and Greenstein ([^16]). Detailed discussion of scattering and phase functions, along with derivations of phase functions that describe scattering from independent spheres, cylinders, and other simple shapes, can be found in van de Hulst’s book ([^34]). Extensive discussion of the Mie and Rayleigh scattering models is also available there. Hansen and Travis’s survey article is also a good introduction to the variety of commonly used phase functions ([Hansen and Travis 1974](#cite:Hansen74)); see also d’Eon’s book ([^5]) for a catalog of useful phase functions and associated sampling techniques.

While the Henyey–Greenstein model often works well, there are many media that it cannot represent accurately. Gkioulekas et al. ([2013a](#cite:Gkioulekas2013a)) showed that sums of Henyey–Greenstein and von Mises-Fisher lobes are more accurate for representing scattering in many materials than Henyey–Greenstein alone and derived a 2D parameter space that allows for intuitive control of translucent appearance.

The paper by Raab et al. ([^30]) introduced many important sampling building-blocks for rendering participating media to graphics, including the delta-tracking algorithm for inhomogeneous media. Delta tracking has been independently invented in a number of fields; see both Kutz et al. ([^20]) and Kettunen et al. ([^18]) for further details of this history.

The ratio tracking algorithm was introduced to graphics by Novák et al. ([^24]), though see the discussion in Novák et al. ([^25]) for the relationship of this approach to previously developed estimators in neutron transport. Novák et al. ([^24]) also introduced *residual ratio tracking*, which makes use of lower bounds on a medium’s density to analytically integrate part of the beam transmittance. Kutz et al. ([^20]) extended this approach to distance sampling and introduced the integral formulation of transmittance due to Galtier et al. ([^12]). Our derivation of the integral transmittance equations ([11.10](https://pbr-book.org/4ed/Volume_Scattering/Transmittance.html#eq:volterra-transmittance)) and ([11.13](https://pbr-book.org/4ed/Volume_Scattering/Transmittance.html#eq:volterra-null-transmittance)) follows Georgiev et al. ([^13]), as does our discussion of connections between those equations and various transmittance estimators. Georgiev at al. also developed a number of additional estimators for transmittance that can give significantly lower error than the ratio tracking estimator that pbrt uses.

Kettunen et al. ([^18]) recently developed a significantly improved transmittance estimator with much lower error than previous approaches. Remarkably, their estimator is effectively a combination of uniform ray marching with a correction term that removes bias.

For media with substantial variation in density, delta tracking can be inefficient—many small steps must be taken to get through the optically thin sections. Danskin and Hanrahan ([^6]) presented a technique for efficient volume ray marching using a hierarchical data structure. Another way of addressing this issue was presented by Szirmay-Kalos et al. ([^31]), who used a grid to partition scattering volumes in cells and applied delta tracking using the majorant of each cell as the ray passed through them. This is effectively the approach implemented in pbrt ’s [DDAMajorantIterator](https://pbr-book.org/4ed/Volume_Scattering/Media.html#DDAMajorantIterator). The grid cell traversal algorithm implemented there is due to Cleary and Wyvill ([^3]) and draws from Bresenham’s line drawing algorithm ([Bresenham 1965](#cite:Bresenham1965)). Media stored in grids are sometimes tabulated in the camera’s projective space, making it possible to have more detail close to the camera and less detail farther away. Gamito has recently developed an algorithm for DDA traversal in this case ([Gamito 2021](#cite:Gamito2021)).

Yue et al. ([^37]) used a kd-tree to store majorants, which was better able to adapt to spatially varying densities than a grid. In follow-on work, they derived an approach to estimate the efficiency of spatial partitionings and used it to construct them more effectively (Yue et al. [^38]).

Because scattering may be sampled rarely in optically thin media, many samples may be necessary to achieve low error. To address this issue, Villemin et al. proposed increasing the sampling density in such media ([Villemin et al. 2018](#cite:Villemin2018)).

Kulla and Fajardo ([^19]) noted that techniques based on sampling according to transmittance ignore another important factor: spatial variation in the scattering coefficient. They developed a method based on computing a tabularized 1D sampling distribution for each ray passing through participating media based on the product of beam transmittance and scattering coefficient at a number of points along it. They then drew samples from this distribution, showing good results.

A uniform grid of sample values as is implemented in [GridMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#GridMedium) and [RGBGridMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#RGBGridMedium) may consume an excessive amount of memory, especially for media that have not only large empty regions of space but also fine detail in some regions. This issue is addressed by Museth’s VDB format ([^22]) as well as the Field3D system that was described by Wrenninge ([^36]), both of which use adaptive hierarchical grids to reduce storage requirements. pbrt ’s [NanoVDBMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#NanoVDBMedium) is based on NanoVDB ([Museth 2021](#cite:Museth21)), which is a lighterweight version of VDB.

Just as procedural modeling of textures is an effective technique for shading surfaces, procedural modeling of volume densities can be used to describe realistic-looking volumetric objects like clouds and smoke. Perlin and Hoffert ([^27]) described early work in this area, and the book by Ebert et al. ([^7]) has a number of sections devoted to this topic, including further references. More recently, accurate physical simulation of the dynamics of smoke and fire has led to extremely realistic volume data sets, including the ones used in this chapter; for early work in this area, see for example Fedkiw, Stam, and Jensen ([^8]). The book by Wrenninge ([^35]) has further information about modeling participating media, with particular focus on techniques used in modern feature film production.

For media that are generated through simulations, it may be desirable to account for the variation in the medium over time in order to include the effect of motion blur. Clinton and Elendt ([^4]) described an approach to do so based on deforming the vertices of the grid that stores the medium, and Kulla and Fajardo ([^19]) applied Eulerian motion blur, where each grid cell also stores a velocity vector that is used to shift the lookup point based on its time. Wrenninge described a more efficient approach that instead stores the scattering properties in each cell as a compact time-varying function ([Wrenninge 2016](#cite:Wrenninge2016)).

In this chapter, we have ignored all issues related to sampling and antialiasing of volume density functions that are represented by samples in a 3D grid, although these issues should be considered, especially in the case of a volume that occupies just a few pixels on the screen. Furthermore, we have used a simple triangle filter to reconstruct densities at intermediate positions, which is suboptimal for the same reasons that the triangle filter is not a high-quality image reconstruction filter. Marschner and Lobb ([^21]) presented the theory and practice of sampling and reconstruction for 3D data sets, applying ideas similar to those in Chapter [8](https://pbr-book.org/4ed/Sampling_and_Reconstruction.html#chap:sampling-reconstruction). See also the paper by Theußl, Hauser, and Gröller ([^32]) for a comparison of a variety of windowing functions for volume reconstruction with the sinc function and a discussion of how to derive optimal parameters for volume reconstruction filter functions.

Hofmann et al. ([^17]) noted that sample reconstruction may have a significant performance cost, even with trilinear filtering. They suggested *stochastic sample filtering*, where a single volume sample is chosen with probability given by its filter weight, and showed performance benefits. However, this approach does introduce bias if a nonlinear function is applied to the sample value (as is the case when estimating transmittance, for example).

Acquiring volumetric scattering properties of real-world objects is particularly difficult, requiring a solution to the inverse problem of determining the values that lead to the measured result. See Jensen et al. ([2001b](#cite:Jensen01)), Goesele et al. ([^14]), Narasimhan et al. ([^23]), and Peers et al. ([^26]) for work on acquiring scattering properties for subsurface scattering. More recently, Gkioulekas et al. ([2013b](#cite:Gkioulekas2013b)) produced accurate measurements of a variety of media. Hawkins et al. ([^15]) have developed techniques to measure properties of media like smoke, acquiring measurements in real time. Another interesting approach to this problem was introduced by Frisvad et al. ([^10]), who developed methods to compute these properties from a lower-level characterization of the scattering properties of the medium. A comprehensive survey of work in this area was presented by Frisvad et al. ([^9]). (See also the discussion of inverse rendering techniques in Section [16.3.1](https://pbr-book.org/4ed/Retrospective_and_the_Future/Emerging_Topics.html#sec:differentiable-rendering) for additional approaches to these problems.)

Acquiring the volumetric density variation of participating media is also challenging. See work by Fuchs et al. ([^11]), Atcheson et al. ([^1]), and Gu et al. ([2013a](#cite:Gu2013a)) for a variety of approaches to this problem, generally based on illuminating the medium in particular ways while photographing it from one or more viewpoints.

### References

[^1]: Atcheson, B., I. Ihrke, W. Heidrich, A. Tevs, D. Bradley, M. Magnor, and H.-P. Seidel. 2008. Time-resolved 3d capture of non-stationary gas flows. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *27*  (5), 132:1–9.

[^2]: Chandrasekhar, S. 1960. *Radiative Transfer*. New York: Dover Publications. Originally published by Oxford University Press, 1950.

[^3]: Cleary, J. G., and G. Wyvill. 1988. Analysis of an algorithm for fast ray tracing using uniform space subdivision. *The Visual Computer* *4*  (2), 65–83.

[^4]: Clinton, A., and M. Elendt. 2009. Rendering volumes with microvoxels. *SIGGRAPH 2009 Talks*, 47:1.

[^5]: d’Eon, E. 2016. *A Hitchhiker’s Guide to Multiple Scattering*. [http://www.eugenedeon.com/hitchhikers](http://www.eugenedeon.com/hitchhikers)

[^6]: Danskin, J., and P. Hanrahan. 1992. Fast algorithms for volume ray tracing. In *1992 Workshop on Volume Visualization*, 91–98.

[^7]: Ebert, D., F. K. Musgrave, D. Peachey, K. Perlin, and S. Worley. 2003. *Texturing and Modeling: A Procedural Approach*. San Francisco: Morgan Kaufmann.

[^8]: Fedkiw, R., J. Stam, and H. W. Jensen. 2001. Visual simulation of smoke. *Proceedings of ACM SIGGRAPH 2001*, Computer Graphics Proceedings, Annual Conference Series, 15–22.

[^9]: Frisvad, J. R., S. A. Jensen, J. S. Madsen, A. Correia, L. Yang, S. K. S. Gregersen, Y. Meuret, and P.-E. Hansen. 2020. Survey of models for acquiring the optical properties of translucent materials. *Computer Graphics Forum (Eurographics State of the Art Report)* *39*  (2), 729–55.

[^10]: Frisvad, J., N. Christensen, and H. W. Jensen. 2007. Computing the scattering properties of participating media using Lorenz-Mie theory. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2007)* *26*  (3), 60:1–10.

[^11]: Fuchs, C., T. Chen, M. Goesele, H. Theisel, and H.-P. Seidel. 2007. Density estimation for dynamic volumes. *Computers and Graphics* *31*  (2), 205–11.

[^12]: Galtier, M., S. Blanco, C. Caliot, C. Coustet, J. Dauchet, M. El Hafi, V. Eymet, R. Fournier, J. Gautrais, A. Khuong, B. Piaud, and G. Terrée. 2013. Integral formulation of null-collision Monte Carlo algorithms. *Journal of Quantitative Spectroscopy and Radiative Transfer* *125*, 57–68.

[^13]: Georgiev, I., Z. Misso, T. Hachisuka, D. Nowrouzezahrai, J. Křivánek, and W. Jarosz. 2019. Integral formulations of volumetric transmittance. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *38*  (6), 154:1–17.

[^14]: Goesele, M., H. Lensch, J. Lang, C. Fuchs, and H.-P. Seidel. 2004. DISCO—Acquisition of translucent objects. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2004)* *23*  (3), 844–53.

[^15]: Hawkins, T., P. Einarsson, and P. Debevec. 2005. Acquisition of time-varying participating media. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2005)* *24*  (3), 812–15.

[^16]: Henyey, L. G., and J. L. Greenstein. 1941. Diffuse radiation in the galaxy. *Astrophysical Journal* *93*, 70–83.

[^17]: Hofmann, N., J. Hasselgren, P. Clarberg, and J. Munkberg. 2021. Interactive path tracing and reconstruction of sparse volumes. *Proceedings of the ACM on Computer Graphics and Interactive Techniques* *4*  (1), 5:1–19.

[^18]: Kettunen, M., E. d’Eon, J. Pantaleoni, and J. Novák. 2021. An unbiased ray-marching transmittance estimator. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *40*  (4), 137:1–20.

[^19]: Kulla, C., and M. Fajardo. 2012. Importance sampling techniques for path tracing in participating media. *Computer Graphics Forum (Proceedings of the 2012 Eurographics Symposium on Rendering)* *31*  (4), 1519–28.

[^20]: Kutz, P., R. Habel, Y. K. Li, and J. Novák. 2017. Spectral and decomposition tracking for rendering heterogeneous volumes. *ACM Transactions on Graphics* *36*  (4), 111:1–16.

[^21]: Marschner, S. R., and R. J. Lobb. 1994. An evaluation of reconstruction filters for volume rendering. In *Proceedings of Visualization ’94*, 100–107.

[^22]: Museth, K. 2013. VDB: High-resolution sparse volumes with dynamic topology. *ACM Transactions on Graphics* *32*  (3), 27:1–22.

[^23]: Narasimhan, S., M. Gupta, C. Donner, R. Ramamoorthi, S. Nayar, and H. W. Jensen. 2006. Acquiring scattering properties of participating media by dilution. *ACM Transactions on Graphics* *25*  (3), 1003–12.

[^24]: Novák, J., A. Selle, and W. Jarosz. 2014. Residual ratio tracking for estimating attenuation in participating media. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2014)* *33*  (6), 179:1–11.

[^25]: Novák, J., I. Georgiev, J. Hanika, and W. Jarosz. 2018. Monte Carlo methods for volumetric light transport simulation. *Computer Graphics Forum (Presented at Eurographics 2018–State of the Art Report)* *37*  (2), 551–76.

[^26]: Peers, P., K. vom Berge, W. Matusik, R. Ramamoorthi, J. Lawrence, S. Rusinkiewicz, and P. Dutré. 2006. A compact factored representation of heterogeneous subsurface scattering. *ACM Transactions on Graphics* *25*  (3), 746–53.

[^27]: Perlin, K., and E. M. Hoffert. 1989. Hypertexture. In *Computer Graphics (Proceedings of SIGGRAPH ’89)*, Volume 23, 253–62.

[^28]: Preisendorfer, R. W. 1965. *Radiative Transfer on Discrete Spaces*. Oxford: Pergamon Press.

[^29]: Preisendorfer, R. W. 1976. *Hydrologic Optics*. Honolulu, Hawaii: U.S. Department of Commerce, National Oceanic and Atmospheric Administration.

[^30]: Raab, M., D. Seibert, and A. Keller. 2006. Unbiased global illumination with participating media. *Proc. Monte Carlo and Quasi-Monte Carlo Methods 2006*, 591–605.

[^31]: Szirmay-Kalos, L., B. Tóth, M. Magdics. 2011. Free path sampling in high resolution inhomogeneous participating media. *Computer Graphics Forum* *30*  (1), 85–97.

[^32]: Theußl, T., H. Hauser, and E. Gröller. 2000. Mastering windows: Improving reconstruction. In *Proceedings of the 2000 IEEE Symposium on Volume Visualization*, 101–8. New York: ACM Press.

[^33]: van de Hulst, H. C. 1980. *Multiple Light Scattering*. New York: Academic Press.

[^34]: van de Hulst, H. C. 1981. *Light Scattering by Small Particles*. New York: Dover Publications. Originally published by John Wiley & Sons, 1957.

[^35]: Wrenninge, M. 2012. *Production Volume Rendering: Design and Implementation*. Boca Raton, Florida: A. K. Peters/CRC Press.

[^36]: Wrenninge, M. 2015. Field3D. [http://magnuswrenninge.com/field3d](http://magnuswrenninge.com/field3d)

[^37]: Yue, Y., K. Iwasaki, B.-Y. Chen, Y. Dobashi, and T. Nishita. 2010. Unbiased, adaptive stochastic sampling for rendering inhomogeneous participating media. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2010)* *29*  (5), 177:1–7.

[^38]: Yue, Y., K. Iwasaki, B.-Y. Chen, Y. Dobashi, and T. Nishita. 2011. Toward optimal space partitioning for unbiased, adaptive free path sampling of inhomogeneous participating media. *Computer Graphics Forum* *30*  (7), 1911–19.