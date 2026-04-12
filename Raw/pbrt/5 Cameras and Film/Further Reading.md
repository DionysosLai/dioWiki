---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Cameras_and_Film/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
In his seminal *Sketchpad* system, Sutherland ([^44]) was the first to use projection matrices for computer graphics. Akenine-Möller et al. ([^2]) have provided a particularly well-written derivation of the orthographic and perspective projection matrices. Other good references for projections are Rogers and Adams’s *Mathematical Elements for Computer Graphics* ([^35]) and Eberly’s book ([^12]) on game engine design. See Adams and Levoy ([^1]) for a broad analysis of the types of radiance measurements that can be taken with cameras that have non-pinhole apertures.

An unusual projection method was used by Greene and Heckbert ([^17]) for generating images for OMNIMAX® theaters.

Potmesil and Chakravarty ([^31], [^32], [^33]) did early work on depth of field and motion blur in computer graphics. Cook and collaborators developed a more accurate model for these effects based on the thin lens model; this is the approach used for the depth of field calculations in Section [5.2.3](https://pbr-book.org/4ed/Cameras_and_Film/Projective_Camera_Models.html#sec:thin-lens) ([Cook et al. 1984](#cite:Cook84); [Cook 1986](#cite:Cook86)). An alternative approach to motion blur was described by Gribel and Akenine-Möller ([^18]), who analytically computed the time ranges of ray–triangle intersections to eliminate stochastic sampling in time.

Kolb, Mitchell, and Hanrahan ([^26]) showed how to simulate complex camera lens systems with ray tracing in order to model the imaging effects of real cameras; the RealisticCamera is based on their approach. Steinert et al. ([^43]) improved a number of details of this simulation, incorporating wavelength-dependent effects and accounting for both diffraction and glare. Joo et al. ([^23]) extended this approach to handle aspheric lenses and modeled diffraction at the aperture stop, which causes some brightening at the edges of the circle of confusion in practice. See the books by Hecht ([^20]) and Smith ([^42]) for excellent introductions to optics and lens systems.

Hullin et al. ([^21]) used polynomials to model the effect of lenses on rays passing through them; they were able to construct polynomials that approximate entire lens systems from polynomial approximations of individual lenses. This approach saves the computational expense of tracing rays through lenses, though for complex scenes, this cost is generally negligible in relation to the rest of the rendering computations. Hanika and Dachsbacher ([^19]) improved the accuracy of this approach and showed how to combine it with bidirectional path tracing. Schrade et al. ([^39]) showed good results with approximation of wide-angle lenses using sparse higher-degree polynomials.

#### Film and Imaging

The film sensor model presented in Section [5.4.2](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#sec:film-sensors) and the [PixelSensor](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#PixelSensor) class implementation are from the *PhysLight* system described by Langlands and Fascione ([^27]). See also Chen et al. ([^9]), who described the implementation of a fairly complete simulation of a digital camera, including the analog-to-digital conversion and noise in the measured pixel values inherent in this process.

Filter importance sampling, as described in Section [8.8](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#sec:image-reconstruction), was described in a paper by Ernst et al. ([^13]). This technique is also proposed in Shirley’s Ph.D. thesis ([^41]).

The idea of storing additional information about the properties of the visible surface in a pixel was introduced by Perlin ([1985a](#cite:Perlin85)) and Saito and Takahashi ([^37]), who also coined the term *G-Buffer*. Shade et al. ([^40]) introduced the generalization of storing information about all the surfaces along each camera ray and applied this representation to view interpolation, using the originally hidden surfaces to handle disocclusion.

Celarek et al. ([^7]) developed techniques for evaluating sampling schemes based on computing both the expectation and variance of MSE and described approaches for evaluating error in rendered images across both pixels and frequencies.

The sampling technique that approximates the XYZ matching curves is due to Radziszewski et al. ([^34]).

The [SpectralFilm](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#SpectralFilm) uses a representation for spectral images in the OpenEXR format that was introduced by Fichet et al. ([^14]).

As discussed in Section [5.4.2](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#sec:white-balance), the human visual system generally factors out the illumination color to perceive surfaces’ colors independently of it. A number of methods have been developed to process photographs to perform white balancing to eliminate the tinge of light source colors; see Gijsenij et al. ([^16]) for a survey. White balancing photographs can be challenging, since the only information available to white balancing algorithms is the final pixel values. In a renderer, the problem is easier, as information about the light sources is directly available; Wilkie and Weidlich ([^47]) developed an efficient method to perform accurate white balancing in a renderer.

#### Denoising

A wide range of approaches have been developed for removing Monte Carlo noise from rendered images. Here we will discuss those that are based on the statistical characteristics of the sample values themselves. In the “Further Reading” section of Chapter [8](https://pbr-book.org/4ed/Sampling_and_Reconstruction.html#chap:sampling-reconstruction), we will discuss ones that derive filters that account for the underlying light transport equations used to form the image. Zwicker et al.’s report ([^50]) has thorough coverage of both approaches to denoising through 2015. We will therefore focus here on some of the foundational work as well as more recent developments.

Lee and Redner ([^28]) suggested using an alpha-trimmed mean filter for this task; it discards some number of samples at the low and high range of the sample values. The median filter, where all but a single sample are discarded, is a special case of it. Jensen and Christensen ([^22]) observed that it can be effective to separate out the contributions to pixel values based on the type of illumination they represent; low-frequency indirect illumination can be filtered differently from high-frequency direct illumination, thus reducing noise in the final image. They developed an effective filtering technique based on this observation.

McCool ([^29]) used the depth, surface normal, and color at each pixel to determine how to blend pixel values with their neighbors in order to better preserve edges in the filtered image. Keller and collaborators introduced the *discontinuity buffer* (Keller [^25]; Wald et al. [^46]). In addition to filtering slowly varying quantities like indirect illumination separately from more quickly varying quantities like surface reflectance, the discontinuity buffer also uses geometric quantities like the surface normal to determine filter extents.

Dammertz et al. ([^10]) introduced a denoising algorithm based on edge-aware image filtering, applied hierarchically so that very wide kernels can be used with good performance. This approach was improved by Schied et al. ([^38]), who used estimates of variance at each pixel to set filter widths and incorporated temporal reuse, using filtered results from the previous frame in a real-time ray tracer. Bitterli et al. ([^4]) analyzed a variety of previous denoising techniques in a unified framework and derived a new approach based on a first-order regression of pixel values. Boughida and Boubekeur ([^5]) described a Bayesian approach based on statistics of all the samples in a pixel, and Vicini et al. ([2019a](#cite:Vicini2019)) considered the problem of denoising “deep” images, where each pixel may contain multiple color values, each at a different depth.

Some filtering techniques focus solely on the outlier pixels that result when the sampling probability in the Monte Carlo estimator is a poor match to the integrand and is far too small for a sample. (As mentioned previously, the resulting pixels are sometimes called “fireflies,” in a nod to their bright transience.) Rushmeier and Ward ([^36]) developed an early technique to address this issue based on detecting outlier pixels and spreading their energy to nearby pixels in order to maintain an unbiased estimate of the true image. DeCoro et al. ([^11]) suggested storing all pixel sample values and then rejecting outliers before filtering them to compute final pixel values. Zirr et al. ([^49]) proposed an improved approach that uses the distribution of sample values at each pixel to detect and reweight outlier samples. Notably, their approach does not need to store all the individual samples, but can be implemented by partitioning samples into one of a small number of image buffers based on their magnitude. More recently, Buisine et al. ([^6]) proposed using a median of means filter, which is effective at removing outliers but has slower convergence than the mean. They therefore dynamically select between the mean and median of means depending on the characteristics of the sample values.

As with many other areas of image processing and understanding, techniques based on machine learning have recently been applied to denoising rendered images. This work started with Kalantari et al. ([^24]), who used relatively small neural networks to determine parameters for conventional denoising filters. Approaches based on deep learning and convolutional neural networks soon followed with Bako et al. ([^3]), Chaitanya et al. ([^8]), and Vogels et al. ([^45]) developing autoencoders based on the u-net architecture ([Ronneberger et al. 2015](#cite:Ronneberger2015)). Xu et al. ([^48]) applied adversarial networks to improve the training of such denoisers. Gharbi et al. ([^15]) showed that filtering the individual samples with a neural network can give much better results than sampling the pixels with the samples already averaged. Munkberg and Hasselgren ([^30]) described an architecture that reduces the memory and computation required for this approach.

### References

[^1]: Adams, A., and M. Levoy. 2007. General linear cameras with finite aperture. In *Rendering Techniques* (*Proceedings of the 2007 Eurographics Symposium on Rendering*), 121–26.

[^2]: Akenine-Möller, T., E. Haines, N. Hoffman, A. Peesce, M. Iwanicki, and S. Hillaire. 2018. *Real-Time Rendering* (4th ed.). Boca Raton, FL: CRC Press.

[^3]: Bako, S., T. Vogels, B. McWilliams, M. Meyer, J. Novák, A. Harvill, P. Sen, T. DeRose, and F. Rousselle. 2017. Kernel-predicting convolutional networks for denoising Monte Carlo renderings. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 97:1–14.

[^4]: Bitterli, B., F. Rousselle, B. Moon, J. A. Iglesias-Guitián, D. Adler, K. Mitchell, W. Jarosz, and J. Novák. 2016. Nonlinearly weighted first-order regression for denoising Monte Carlo renderings. *Computer Graphics Forum* *35*  (4), 107–17.

[^5]: Boughida, M., and T. Boubekeur. 2017. Bayesian collaborative denoising for Monte Carlo rendering. *Computer Graphics Forum* *36*  (4), 137–53.

[^6]: Buisine, J., S. Delepoulle, and C. Renaud. 2021. Firefly removal in Monte Carlo rendering with adaptive Median of meaNs. *Proceedings of the Eurographics Symposium on Rendering*, 121–32.

[^7]: Celarek, A., W. Jakob, M. Wimmer, and J. Lehtinen. 2019. Quantifying the error of light transport algorithms. *Computer Graphics Forum* *38*  (4), 111–21.

[^8]: Chaitanya, C. R. A., A. S. Kaplanyan, C. Schied, M. Salvi, A. Lefohn, D. Nowrouzezahrai, and T. Aila. 2017. Interactive reconstruction of Monte Carlo image sequences using a recurrent denoising autoencoder. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *36*  (4), 98:1–12.

[^9]: Chen, J., K. Venkataraman, D. Bakin, B. Rodricks, R. Gravelle, P. Rao, and Y. Ni. 2009. Digital camera imaging system simulation. *IEEE Transactions on Electron Devices* *56*  (11), 2496–505.

[^10]: Dammertz, H., D. Sewtz, J. Hanika, and H. P. A. Lensch. 2010. Edge-avoiding À-Trous wavelet transform for fast global illumination filtering. *Proceedings of High Performance Graphics (HPG ’10)*, 67–75.

[^11]: DeCoro, C., T. Weyrich, and S. Rusinkiewicz. 2010. Density-based outlier rejection in Monte Carlo rendering. *Computer Graphics Forum (Proceedings of Pacific Graphics)* *29*  (7), 2119–25.

[^12]: Eberly, D. H. 2001. *3D Game Engine Design: A Practical Approach to Real-Time Computer Graphics*. San Francisco: Morgan Kaufmann.

[^13]: Ernst, M., M. Stamminger, and G. Greiner. 2006. Filter importance sampling. *IEEE Symposium on Interactive Ray Tracing*, 125–32.

[^14]: Fichet, A., R. Pacanowski, and A. Wilkie. 2021. An OpenEXR layout for spectral images. *Journal of Computer Graphics Techniques* *10*  (3), 1–18.

[^15]: Gharbi, M., T.-M. Li, M. Aittala, J. Lehtinen, and F. Durand. 2019. Sample-based Monte Carlo denoising using a kernel-splatting network. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *38*  (4), 125:1–12.

[^16]: Gijsenij, A., T. Gevers, and J. van de Weijer. 2011. Computational color constancy: Survey and experiments. *IEEE Transactions on Image Processing* *20*  (9), 2475–89.

[^17]: Greene, N., and P. S. Heckbert. 1986. Creating raster Omnimax images from multiple perspective views using the elliptical weighted average filter. *IEEE Computer Graphics and Applications* *6*  (6), 21–27.

[^18]: Gribel, C. J., and T. Akenine-Möller. 2017. Time-continuous quasi-Monte Carlo ray tracing. *Computer Graphics Forum* *36*  (6), 354–67.

[^19]: Hanika, J., and C. Dachsbacher. 2014. Efficient Monte Carlo rendering with realistic lenses. *Computer Graphics Forum (Proceedings of Eurographics 2014)* *33*  (2), 323–32.

[^20]: Hecht, E. 2002. *Optics*. Reading, Massachusetts: Addison-Wesley.

[^21]: Hullin, M. B., J. Hanika, and W. Heidrich. 2012. Polynomial optics: A construction kit for efficient ray-tracing of lens systems. *Computer Graphics Forum (Proceedings of the 2012 Eurographics Symposium on Rendering)* *31*  (4), 1375–83.

[^22]: Jensen, H. W., and N. Christensen. 1995. Optimizing path tracing using noise reduction filters. In *Proceedings of WSCG*, 134–42.

[^23]: Joo, H., S. Kwon, S. Lee, E. Eisemann, and S. Lee. 2016. Efficient ray tracing through aspheric lenses and imperfect Bokeh synthesis. *Computer Graphics Forum* *35*  (4), 99–105.

[^24]: Kalantari, N. K., S. Bako, and P. Sen. 2015. A machine learning approach for filtering Monte Carlo noise. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2015)* *34*  (4), 122:1–12.

[^25]: Keller, A. 1998. Quasi-Monte Carlo methods for photorealistic image synthesis. Ph.D. thesis, Shaker Verlag Aachen.

[^26]: Kolb, C., D. Mitchell, and P. Hanrahan. 1995. A realistic camera model for computer graphics. *SIGGRAPH ’95 Conference Proceedings*, Annual Conference Series, 317–24.

[^27]: Langlands, A., and L. Fascione. 2020. PhysLight: An end-to-end pipeline for scene-referred lighting. *SIGGRAPH 2020 Talks* *19*, 191–2.

[^28]: Lee, M., and R. Redner. 1990. A note on the use of nonlinear filtering in computer graphics. *IEEE Computer Graphics and Applications* *10*  (3), 23–29.

[^29]: McCool, M. D. 1999. Anisotropic diffusion for Monte Carlo noise reduction. *ACM Transactions on Graphics* *18*  (2), 171–94.

[^30]: Munkberg, J., and J. Hasselgren. 2020. Neural denoising with layer embeddings. *Computer Graphics Forum* *39*  (4), 1–12.

[^31]: Potmesil, M., and I. Chakravarty. 1981. A lens and aperture camera model for synthetic image generation. In *Computer Graphics (Proceedings of SIGGRAPH ’81)*, Volume 15, 297–305.

[^32]: Potmesil, M., and I. Chakravarty. 1982. Synthetic image generation with a lens and aperture camera model. *ACM Transactions on Graphics* *1*  (2), 85–108.

[^33]: Potmesil, M., and I. Chakravarty. 1983. Modeling motion blur in computer-generated images. In *Computer Graphics (Proceedings of SIGGRAPH 83)*, Volume 17, 389–99.

[^34]: Radziszewski, M., K. Boryczko, and W. Alda. 2009. An improved technique for full spectral rendering. *Journal of WSCG* *17*  (1-3), 9–16.

[^35]: Rogers, D. F., and J. A. Adams. 1990. *Mathematical Elements for Computer Graphics*. New York: McGraw-Hill.

[^36]: Rushmeier, H. E., and G. J. Ward. 1994. Energy preserving non-linear filters. *Proceedings of SIGGRAPH 1994*, 131–38.

[^37]: Saito, T., and T. Takahashi. 1990. Comprehensible rendering of 3-D shapes. In *Computer Graphics (Proceedings of SIGGRAPH ’90)*, Volume 24, 197–206.

[^38]: Schied, S., A. Kaplanyan, C. Wyman, A. Patney, C. R. Alla Chaitanya, J. Burgess, S. Liu, C. Dachsbacher, A. Lefohn, and M. Salvi. 2017. Spatiotemporal variance-guided filtering: Real-time reconstruction for path-traced global illumination. In *Proceedings of High Performance Graphics (HPG ’17)*, 2:1–12.

[^39]: Schrade, E., J. Hanika, and C. Dachsbacher. 2016. Sparse high-degree polynomials for wide-angle lenses. *Computer Graphics Forum* *35*  (4), 89–97.

[^40]: Shade, J., S. J. Gortler, L. W. He, and R. Szeliski. 1998. Layered depth images. In *Proceedings of SIGGRAPH 98*, Computer Graphics Proceedings, Annual Conference Series, 231–42.

[^41]: Shirley, P. 1990. Physically based lighting calculations for computer graphics. Ph.D. thesis, Department of Computer Science, University of Illinois, Urbana–Champaign.

[^42]: Smith, W. 2007. *Modern Optical Engineering* (4th ed.). New York: McGraw-Hill Professional.

[^43]: Steinert, B., H. Dammertz., J. Hanika, and H. P. A. Lensch. General spectral camera lens simulation. 2011. *Computer Graphics Forum* *30*  (6), 1643–54.

[^44]: Sutherland, I. E. 1963. Sketchpad—A man–machine graphical communication system. In *Proceedings of the Spring Joint Computer Conference (AFIPS)*, 328–46.

[^45]: Vogels, T., F. Rousselle, B. McWilliams, G. Röthlin, A. Harvill, D. Adler, M. Meyer, and J. Novák. 2018. Denoising with kernel prediction and asymmetric loss functions. *ACM Transactions on Graphics (Proceedings of SIGGRAPH)* *37*  (4), 124:1–15.

[^46]: Wald, I., T. Kollig, C. Benthin, A. Keller, and P. Slusallek. 2002. Interactive global illumination using fast ray tracing. In *Rendering Techniques 2002: 13th Eurographics Workshop on Rendering*, 15–24.

[^47]: Wilkie, A., and A. Weidlich. 2009. A robust illumination estimate for chromatic adaptation in rendered images. *Computer Graphics Forum (Proceedings of the 2009 Eurographics Symposium on Rendering)* *28*  (4), 1101–9.

[^48]: Xu, B., J. Zhang, R. Wang, K. Xu, Y.-L. Yang, C. Li, and R. Tang. 2019. Adversarial Monte Carlo denoising with conditioned auxiliary feature. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)* *38*  (6), 224:1–12.

[^49]: Zirr, T., J. Hanika, and C. Dachsbacher. 2018. Reweighting firefly samples for improved finite-sample Monte Carlo estimates. *Computer Graphics Forum* *37*  (6), 410–21.

[^50]: Zwicker, M., W. Jarosz, J. Lehtinen, B. Moon, R. Ramamoorthi, F. Rousselle, P. Sen, C. Soler, and S.-E. Yoon. 2015. Recent advances in adaptive sampling and reconstruction for Monte Carlo rendering. *Computer Graphics Forum (Proceedings of Eurographics 2015)* *34*  (2), 667–81.