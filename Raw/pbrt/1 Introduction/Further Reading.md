---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Introduction/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
In a seminal early paper, Arthur Appel ([^2]) first described the basic idea of ray tracing to solve the hidden surface problem and to compute shadows in polygonal scenes. Goldstein and Nagel ([^13]) later showed how ray tracing could be used to render scenes with quadric surfaces. Kay and Greenberg ([^17]) described a ray-tracing approach to rendering transparency, and Whitted’s seminal *CACM* article described a general recursive ray-tracing algorithm that accurately simulates reflection and refraction from specular surfaces and shadows from point light sources ([Whitted 1980](#cite:Whitted80)). Whitted has recently written an article describing developments over the early years of ray tracing ([Whitted 2020](#cite:Whitted2020)).

In addition to the ones discussed in Section [1.6](https://pbr-book.org/4ed/Introduction/A_Brief_History_of_Physically_Based_Rendering.html#sec:pbr-history), notable early books on physically based rendering and image synthesis include Cohen and Wallace’s *Radiosity and Realistic Image Synthesis* ([^7]), Sillion and Puech’s *Radiosity and Global Illumination* ([^25]), and Ashdown’s *Radiosity: A Programmer’s Perspective* ([^3]), all of which primarily describe the finite-element radiosity method. The course notes from the Monte Carlo ray-tracing course at SIGGRAPH have a wealth of practical information (Jensen et al. [2001a](#cite:Jensen01course), [^16]), much of it still relevant, now nearly twenty years later.

In a paper on ray-tracing system design, Kirk and Arvo ([^19]) suggested many principles that have now become classic in renderer design. Their renderer was implemented as a core kernel that encapsulated the basic rendering algorithms and interacted with primitives and shading routines via a carefully constructed object-oriented interface. This approach made it easy to extend the system with new primitives and acceleration methods. pbrt ’s design is based on these ideas.

To this day, a good reference on basic ray-tracer design is *Introduction to Ray Tracing* ([Glassner 1989a](#cite:Glassner:IntroRayTracing)), which describes the state of the art in ray tracing at that time and has a chapter by Heckbert that sketches the design of a basic ray tracer. More recently, Shirley and Morley’s *Realistic Ray Tracing* ([^24]) offers an easy-to-understand introduction to ray tracing and includes the complete source code to a basic ray tracer. Suffern’s book ([^29]) also provides a gentle introduction to ray tracing. Shirley’s *Ray Tracing in One Weekend* series ([^23]) is an accessible introduction to the joy of writing a ray tracer.

Researchers at Cornell University have developed a rendering testbed over many years; its design and overall structure were described by Trumbore, Lytle, and Greenberg ([^31]). Its predecessor was described by Hall and Greenberg ([^15]). This system is a loosely coupled set of modules and libraries, each designed to handle a single task (ray–object intersection acceleration, image storage, etc.) and written in a way that makes it easy to combine appropriate modules to investigate and develop new rendering algorithms. This testbed has been quite successful, serving as the foundation for much of the rendering research done at Cornell through the 1990s.

*Radiance* was the first widely available open source renderer based fundamentally on physical quantities. It was designed to perform accurate lighting simulation for architectural design. Ward described its design and history in a paper and a book ([Ward 1994](#cite:Ward94); [Larson and Shakespeare 1998](#cite:Ward98)). *Radiance* is designed in the UNIX style, as a set of interacting programs, each handling a different part of the rendering process. This general type of rendering architecture was first described by Duff ([^9]).

Glassner’s ([^12]) *Spectrum* rendering architecture also focuses on physically based rendering, approached through a signal-processing-based formulation of the problem. It is an extensible system built with a plug-in architecture; pbrt ’s approach of using parameter/value lists for initializing implementations of the main abstract interfaces is similar to *Spectrum* ’s. One notable feature of *Spectrum* is that all parameters that describe the scene can be functions of time.

Slusallek and Seidel ([^27], [^28]; Slusallek [^26]) described the *Vision* rendering system, which is also physically based and designed to support a wide variety of light transport algorithms. In particular, it had the ambitious goal of supporting both Monte Carlo and finite-element-based light transport algorithms.

Many papers have been written that describe the design and implementation of other rendering systems, including renderers for entertainment and artistic applications. The Reyes architecture, which forms the basis for Pixar’s *RenderMan* renderer, was first described by Cook et al. ([^8]), and a number of improvements to the original algorithm have been summarized by Apodaca and Gritz ([^1]). Gritz and Hahn ([^14]) described the *BMRT* ray tracer. The renderer in the *Maya* modeling and animation system was described by Sung et al. ([^30]), and some of the internal structure of the *mental ray* renderer is described in Driemeyer and Herken’s book on its API ([Driemeyer and Herken 2002](#cite:Driemeyer02)). The design of the high-performance *Manta* interactive ray tracer was described by Bigler et al. ([^4]).

*OptiX* introduced a particularly interesting design approach for high-performance ray tracing: it is based on doing JIT compilation at runtime to generate a specialized version of the ray tracer, intermingling user-provided code (such as for material evaluation and sampling) and renderer-provided code (such as high-performance ray–object intersection). It was described by Parker et al. ([^22]).

More recently, Eisenacher et al. discussed the ray sorting architecture of Disney’s *Hyperion* renderer ([Eisenacher et al. 2013](#cite:Eisenacher2013)), and Lee et al. have written about the implementation of the *MoonRay* rendering system at DreamWorks ([Lee et al. 2017](#cite:Lee2017)). The implementation of the *Iray* ray tracer was described by Keller et al. ([^18]).

In 2018, a special issue of *ACM Transactions on Graphics* included papers describing the implementations of five rendering systems that are used for feature film production. These papers are full of details about the various renderers; reading them is time well spent. They include Burley et al.’s description of Disney’s *Hyperion* renderer ([^5]), Christensen et al. on Pixar’s modern *RenderMan* ([^6]), Fascione et al. describing Weta Digital’s *Manuka* ([^10]), Georgiev et al. on Solid Angle’s version of *Arnold* ([^11]) and Kulla et al. on the version of *Arnold* used at Sony Pictures Imageworks ([^20]).

Whereas standard rendering algorithms generate images from a 3D scene description, the *Mitsuba 2* system is engineered around the corresponding inverse problem. It computes derivatives with respect to scene parameters using JIT-compiled kernels that efficiently run on GPUs and CPUs. These kernels are then used in the inner loop of an optimization algorithm to reconstruct 3D scenes that are consistent with user-provided input images. This topic is further discussed in Section [16.3.1](https://pbr-book.org/4ed/Retrospective_and_the_Future/Emerging_Topics.html#sec:differentiable-rendering). The system’s design and implementation was described by Nimier-David et al. ([^21]).

### References

[^1]: Apodaca, A. A., and L. Gritz. 2000. *Advanced RenderMan: Creating CGI for Motion Pictures.* San Francisco: Morgan Kaufmann.

[^2]: Appel, A. 1968. Some techniques for shading machine renderings of solids. In *AFIPS 1968 Spring Joint Computer Conference* *32*, 37–45.

[^3]: Ashdown, I. 1994. *Radiosity: A Programmer’s Perspective*. New York: John Wiley & Sons.

[^4]: Bigler, J., A. Stephens, and S. Parker. 2006. Design for parallel interactive ray tracing systems. *IEEE Symposium on Interactive Ray Tracing*, 187–95.

[^5]: Burley, B., D. Adler, M. J-Y. Chiang, H. Driskill, R. Habel, P. Kelly, P. Kutz, Y. K. Li, and D. Teece. 2018. The design and evolution of Disney’s Hyperion renderer. *ACM Transactions on Graphics* *37*  (3), 33:1–22.

[^6]: Christensen, P., J. Fong, J. Shade, W. Wooten, B. Schubert, A. Kensler, S. Friedman, C. Kilpatrick, C. Ramshaw, M. Bannister, B. Rayner, J. Brouillat, and M. Liani. 2018. RenderMan: An advanced path-tracing architecture for movie rendering. *ACM Transactions on Graphics* *37*  (3), 30:1–21.

[^7]: Cohen, M., and J. Wallace. 1993. *Radiosity and Realistic Image Synthesis*. San Diego: Academic Press Professional.

[^8]: Cook, R. L., L. Carpenter, and E. Catmull. 1987. The Reyes image rendering architecture. *Computer Graphics (Proceedings of SIGGRAPH ’87)* *21*  (4), 95–102.

[^9]: Duff, T. 1985. Compositing 3-D rendered images. *Computer Graphics (Proceedings of SIGGRAPH ’85)* *19*, 41–44.

[^10]: Fascione, L., J. Hanika, M. Leone, M. Droske, J. Schwarzhaupt, T. Davidovič, A. Weidlich, and J. Meng. 2018. Manuka: A batch-shading architecture for spectral path tracing in movie production. *ACM Transactions on Graphics* *37*  (3), 31:1–18.

[^11]: Georgiev, I., T. Ize, M. Farnsworth, R. Montoya-Vozmediano, A. King, B. Van Lommel, A. Jimenez, O. Anson, S. Ogaki, E. Johnston, A. Herubel, D. Russell, F. Servant, and M. Fajardo. 2018. Arnold: A brute-force production path tracer. *ACM Transactions on Graphics* *37*  (3), 32:1–12.

[^12]: Glassner, A. 1993. Spectrum: An architecture for image synthesis, research, education, and practice. *Developing Large-Scale Graphics Software Toolkits, SIGGRAPH ’93 Course Notes*, *3*, 1:14–43.

[^13]: Goldstein, R. A., and R. Nagel. 1971. 3-D visual simulation. *Simulation* *16*  (1), 25–31.

[^14]: Gritz, L., and J. K. Hahn. 1996. BMRT: A global illumination implementation of the RenderMan standard. *Journal of Graphics Tools* *1*  (3), 29–47.

[^15]: Hall, R. A., and D. P. Greenberg. 1983. A testbed for realistic image synthesis. *IEEE Computer Graphics and Applications* *3*  (8), 10–20.

[^16]: Jensen, H. W., J. Arvo, P. Dutré, A. Keller, A. Owen, M. Pharr, and P. Shirley. 2003. Monte Carlo ray tracing. In *SIGGRAPH 2003 Courses*, San Diego.

[^17]: Kay, D. S., and D. P. Greenberg. 1979. Transparency for computer synthesized images. In *Computer Graphics (SIGGRAPH ’79 Proceedings)*, Volume 13, 158–64.

[^18]: Keller, A., C. Wächter, M. Raab, D. Seibert, D. van Antwerpen, J. Korndörfer, and L. Kettner. 2017. The Iray light transport simulation and rendering system. arXiv:1705.01263 \[cs.GR\].

[^19]: Kirk, D., and J. Arvo. 1988. The ray tracing kernel. In *Proceedings of Ausgraph ’88*, 75–82.

[^20]: Kulla, C., A. Conty, C. Stein, and L. Gritz. 2018. Sony Pictures Imageworks Arnold. *ACM Transactions on Graphics* *37*  (3), 29:1–18.

[^21]: Nimier-David, M., D. Vicini, T. Zeltner, W. Jakob. 2019. Mitsuba 2: A retargetable forward and inverse renderer. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2019)* *38*  (6), 203:1–17.

[^22]: Parker, S. G., J. Bigler, A. Dietrich, H. Friedrich, J. Hoberock, D. Luebke, D. McAllister, M. McGuire, K. Morley, A. Robison, and M. Stich. 2010. OptiX: A general purpose ray tracing engine. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2010)* *29*  (4), 66:1–13.

[^23]: Shirley, P. 2020. Ray Tracing in One Weekend Series. [https://raytracing.github.io/](https://raytracing.github.io/)

[^24]: Shirley, P., and R. K. Morley. 2003. *Realistic Ray Tracing*. Natick, Massachusetts: A. K. Peters.

[^25]: Sillion, F., and C. Puech. 1994. *Radiosity and Global Illumination*. San Francisco: Morgan Kaufmann.

[^26]: Slusallek, P. 1996. Vision—An architecture for physically-based rendering. Ph.D. thesis, University of Erlangen.

[^27]: Slusallek, P., and H.-P. Seidel. 1995. Vision—An architecture for global illumination calculations. *IEEE Transactions on Visualization and Computer Graphics* *1*  (1), 77–96.

[^28]: Slusallek, P., and H.-P. Seidel. 1996. Towards an open rendering kernel for image synthesis. In *Eurographics Rendering Workshop 1996*, 51–60.

[^29]: Suffern, K. 2007. *Ray Tracing from the Ground Up*. Natick, Massachusetts: A. K. Peters.

[^30]: Sung, K., J. Craighead, C. Wang, S. Bakshi, A. Pearce, and A. Woo. 1998. Design and implementation of the Maya renderer. In *Pacific Graphics ’98*

[^31]: Trumbore, B., W. Lytle, and D. P. Greenberg. 1993. A testbed for image synthesis. In *Developing Large-Scale Graphics Software Toolkits*, SIGGRAPH ’93 Course Notes, Volume 3, 4-7–19.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[光线追踪]]
- [[文学编程]]

**同章节**:
- [[1 Introduction]]
- [[1.1 Literate Programming]]
- [[1.2 Photorealistic Rendering and the Ray-Tracing Algorithm]]
- [[1.3 pbrt System Overview]]
- [[1.4 How to Proceed through This Book]]
- [[1.6 A Brief History of Physically Based Rendering]]
- [[1.5 Using and Understanding the Code]]
