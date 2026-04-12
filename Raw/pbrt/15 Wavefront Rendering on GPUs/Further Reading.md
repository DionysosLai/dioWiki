---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
Purcell et al. ([^25], [^24]) and Carr, Hall, and Hart ([^4]) were the first to map general-purpose ray tracers to graphics processors.

A classic paper by Aila and Laine ([^1]) carefully analyzed the performance of ray tracing on contemporary GPUs and developed improved traversal algorithms based on their insights. Follow-on work by Laine et al. ([^17]) discussed the benefits of the wavefront architecture for rendering systems that support a wide variety of materials, textures, and lights. (The use of a wavefront approach for the path tracer described in this chapter is motivated by Laine et al.’s insights.)

Most work in performance optimization for GPU ray tracers analyzes the balance between improving thread execution and memory convergence versus the cost of reordering work to do so. Influential early work includes Hoberock et al. ([^13]), who re-sorted a large number of intersection points to create coherent collections of work before executing their surface shaders. Novák et al. ([^21]) introduced *path regeneration* to start tracing new ray paths in threads that are otherwise idle due to ray termination. Wald ([^35]) and van Antwerpen ([^30]) both applied compaction, densely packing the active threads in thread groups.

Lier et al. ([2018b](#cite:Lier2018:simd)) considered the unconventional approach of distributing the work for a single ray across multiple GPU threads and showed performance benefits for incoherent rays. (This approach parallels how computation is often mapped to CPU SIMD units for high-performance ray tracing.)

Reordering the rays to be traced can also improve performance by improving the coherence of memory accesses performed during intersection tests. Early work in this area was done by Garanzha and Loop ([^10]) and Costa et al. ([^5]). Meister et al. ([^20]) have recently examined ray reordering in the context of a GPU with hardware-accelerated intersection testing and found benefits from using it.

An alternative to taking an arbitrary set of rays and finding structure in them is to generate rays that are inherently coherent in the first place. Examples include the algorithms of Szirmay-Kalos and Purgathofer ([^29]) and Hachisuka ([^12]), which select a single direction for all indirect rays at each level, allowing the use of a rasterizer with parallel projection to trace them. More generally, adding structure to the sample values used for importance sampling can lead to coherence in the rays that are traced. Keller and Heidrich ([^15]) developed interleaved sampling patterns that reuse sample values at separated pixels in order to trade off sample coherence and variation, and Sadeghi et al. ([^26]) investigated the combination of interleaved sampling and using the same pseudo-random sequence at nearby pixels to increase ray coherence. Dufay et al. ([^9]) randomized samples using small random offsets so that nearby pixels still have similar sample values.

Efficient GPU-based construction of acceleration structures is challenging due to the degree of parallelism required; there has been much research on this topic. See Zhou et al. ([^38]), Lauterbach et al. ([^18]), Pantaleoni and Luebke ([^22]), Garanzha et al. ([^11]), Karras and Aila ([^14]), Domingues and Pedrini ([^7]), and Vinkler et al. ([^34]) for techniques for building kd-trees and BVHs on GPUs. See also the “Further Reading” section in Chapter [7](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration.html#chap:acceleration) for additional discussion of algorithms for constructing and traversing acceleration structures on the GPU.

The relatively limited amount of on-chip memory that GPUs have can make it challenging to efficiently implement light transport algorithms that require more than a small amount of storage for each ray. (For example, even storing all the vertices of a pair of subpaths for a bidirectional path-tracing algorithm is much more than a thread could ask to keep on-chip.) The paper by Davidovič et al. ([^6]) gives a thorough overview of these issues and previous work and includes a discussion of implementations of a number of sophisticated light transport algorithms on the GPU.

Zellmann and Lang used compile time polymorphism in C++ to improve the performance of a GPU ray tracer ([Zellmann and Lang 2017](#cite:Zellmann2017)); our implementation in this chapter is based on similar ideas. Zhang et al. ([^37]) compared a number of approaches for dynamic function dispatch on GPUs and evaluated their performance.

Fewer papers have been written about the design of full ray-tracing–based rendering systems on the GPU than on the CPU. Notable papers in this area include Pantaleoni et al.’s ([^23]) description of *PantaRay*, which was used to compute occlusion and lighting by Weta Digital, and Keller et al.’s ([^16]) discussion of the architecture of the *Iray* rendering system. Bikker and van Schijndel ([^3]) described *Brigade*, which targets path-traced games, balancing work between the CPU and GPU and adapting the workload to maintain the desired frame rate.

#### Ray-Tracing Hardware

While all the stages of ray-tracing calculations—construction of the acceleration hierarchy, traversal of the hierarchy, and ray–primitive intersections, as well as shading, lighting, and integration calculations—can be implemented in software on GPUs, there has long been interest in designing specialized hardware for ray–primitive intersection tests and construction and traversal of the acceleration hierarchy for better performance. Deng et al.’s survey article has thorough coverage of hardware acceleration of ray tracing through 2017 ([Deng et al. 2017](#cite:Deng2017)); here, we will focus on early work and more recent developments.

Early published work in this area includes a paper by Woop et al. ([^36]), who described the design of a “ray processing unit” (RPU). Aila and Karras ([^2]) described general architectural issues related to handling incoherent rays, as are common with global illumination algorithms. More recently, Shkurko et al. ([^28]) and Vasiou et al. ([^31]) have described a hardware architecture that is based on reordering ray intersection computation so that it exhibits predictable streaming memory accesses.

Doyle et al. ([^8]) did early work on SAH BVH construction using specialized hardware. Viitanen et al. ([^33], [^32]) have done additional work in this area, designing architectures for efficient HLBVH construction for animated scenes and for high-quality SAH-based BVH construction.

Imagination Technologies announced a mobile GPU that would use a ray-tracing architecture from Caustic (McCombe [^19]), though it never shipped in volume. The NVIDIA Turing architecture ([NVIDIA 2018](#cite:NVIDIA2018)) is the first GPU with hardware-accelerated ray tracing that has seen widespread adoption. The details of its ray-tracing hardware architecture are not publicly documented, though Sanzharov et al. ([^27]) have applied targeted benchmarks to measure its performance characteristics in order to develop hypotheses about its implementation.

### References

[^1]: Aila, T., and S. Laine. 2009. Understanding the efficiency of ray traversal on GPUs. In *Proceedings of High Performance Graphics 2009*, 145–50.

[^2]: Aila, T., and T. Karras. 2010. Architecture considerations for tracing incoherent rays. In *Proceedings of High Performance Graphics 2010*, 113–22.

[^3]: Bikker, J., and J. van Schijndel. 2013. The Brigade renderer: A path tracer for real-time games. *International Journal of Computer Games Technology*, Volume 8.

[^4]: Carr, N., J. D. Hall, and J. Hart. 2002. The ray engine. In *Proceedings of ACM SIGGRAPH Workshop on Graphics Hardware 2002*, 37–46.

[^5]: Costa, V., J. M. Pereira, and J. A. Jorge. 2015. Accelerating occlusion rendering on a GPU via ray classification. *International Journal of Creative Interfaces and Computer Graphics* *6*  (2), 1–17.

[^6]: Davidovič, T., J. Křivánek, M. Hašan, and P. Slusallek. 2014. Progressive light transport simulation on the GPU: Survey and improvements. *ACM Transactions on Graphics* *33*  (3), 29:1–19.

[^7]: Domingues, L. R., and H. Pedrini. 2015. Bounding volume hierarchy optimization through agglomerative treelet restructuring. *Proceedings of High Performance Graphics (HPG ’15)*, 13–20.

[^8]: Doyle, M. J., C. Fowler, and M. Manzke. 2013. A hardware unit for fast SAH-optimised BVH construction. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2013)* *32*  (4), 139:1–10.

[^9]: Dufay, D., P. Lecocq, R. Pacanowski, J.-E. Marvie, and X. Granier. 2016. Cache-friendly micro-jittered sampling. *SIGGRAPH 2016 Talks*, 36:1–2.

[^10]: Garanzha, K., and C. Loop. 2010. Fast ray sorting and breadth-first packet traversal for GPU ray tracing. *Computer Graphics Forum* *29*  (2), 289–98.

[^11]: Garanzha, K., J. Pantaleoni, D. McAllister. 2011. Simpler and faster HLBVH with work queues. *Proceedings of High Performance Graphics 2011*, 59–64.

[^12]: Hachisuka, T. 2005. High-quality global illumination rendering using rasterization. In M. Pharr (ed.), *GPU Gems II: Programming Techniques for High-Performance Graphics and General-Purpose Computation*, 615–34. Reading, Massachusetts: Addison-Wesley.

[^13]: Hoberock, J., V. Lu, Y. Jia, J. Hart. 2009. Stream compaction for deferred shading. In *Proceedings of High Performance Graphics 2009*, 173–80.

[^14]: Karras, T., and T. Aila. 2013. Fast parallel construction of high-quality bounding volume hierarchies. In *Proceedings of High Performance Graphics 2013*, 89–99.

[^15]: Keller, A., and W. Heidrich. 2001. Interleaved sampling. *Proceedings of the 12th Eurographics Workshop on Rendering Techniques*, 269–76.

[^16]: Keller, A., C. Wächter, M. Raab, D. Seibert, D. van Antwerpen, J. Korndörfer, and L. Kettner. 2017. The Iray light transport simulation and rendering system. arXiv:1705.01263 \[cs.GR\].

[^17]: Laine, S., T. Karras, and T. Aila. 2013. Megakernels considered harmful: Wavefront path tracing on GPUs. In *Proceedings of the Fifth High-Performance Graphics Conference (HPG ’13)*, 137–43.

[^18]: Lauterbach, C., M. Garland, S. Sengupta, D. Luebke, and D. Manocha. 2009. Fast BVH construction on GPUs. *Computer Graphics Forum (Eurographics 2009 Conference Proceedings)* *28*  (2), 422–30.

[^19]: McCombe, J. 2013. Low power consumption ray tracing. *SIGGRAPH 2013 Course: Ray Tracing Is the Future and Ever Will Be*

[^20]: Meister, D., J. Boksansky, M. Guthe, and J. Bittner. 2020. On ray reordering techniques for faster GPU ray tracing. *Symposium on Interactive 3D Graphics and Games (I3D ’20)*, 13:1–9.

[^21]: Novák, J., V. Havran, and C. Daschbacher. 2010. Path regeneration for interactive path tracing. *Eurographics 2010 Short Papers*, 61–64.

[^22]: Pantaleoni, J., and D. Luebke. 2010. HLBVH: Hierarchical LBVH construction for real-time ray tracing of dynamic geometry. In *Proceedings of the Conference on High Performance Graphics 2010*, 87–95.

[^23]: Pantaleoni, J., L. Fascione, M. Hill, and T. Aila. 2010. PantaRay: Fast ray-traced occlusion caching of massive scenes. *ACM Transactions on Graphics (Proceedings of SIGGRAPH 2010)* *29*  (4), 37:1–10.

[^24]: Purcell, T. J., C. Donner, M. Cammarano, H. W. Jensen, and P. Hanrahan. 2003. Photon mapping on programmable graphics hardware. In *Graphics Hardware 2003*, 41–50.

[^25]: Purcell, T. J., I. Buck, W. R. Mark, and P. Hanrahan. 2002. Ray tracing on programmable graphics hardware. *ACM Transactions on Graphics* *21*  (3), 703–12.

[^26]: Sadeghi, I., B. Chen, and H. W. Jensen. 2009. Coherent path tracing. *Journal of Graphics, GPU & Game Tools* *14*  (2), 33–43.

[^27]: Sanzharov, V. V., V. A. Frolov, and V. A. Galaktionov. 2020. Survey of NVIDIA RTX Technology. *Programming and Computer Software* *46*  (4), 297–304.

[^28]: Shkurko, K., T. Grant, D. Kopta, I. Mallett, C. Yuksel, and E. Brunvand. 2017. Dual streaming for hardware-accelerated ray tracing. *Proceedings of High Performance Graphics (HPG ’17)*, 12:1–11.

[^29]: Szirmay-Kalos, L., and W. Purgathofer. 1998. Global ray-bundle tracing with hardware acceleration. *Rendering Techniques ’98: 9th Eurographics Workshop on Rendering*, 247–58.

[^30]: van Antwerpen, D. 2011. Improving SIMD efficiency for parallel Monte Carlo light transport on the GPU. *Proceedings of the High Performance Graphics (HPG ’11)*, 41–50.

[^31]: Vasiou, E., K. Shkurko, E. Brunvand, and C. Yuksel. 2019. Mach-RT: A many chip architecture for ray tracing. *High Performance Graphics—Short Papers*, 1–6.

[^32]: Viitanen, T., M. Koskela, P. Jääskeläinen, A. Tervo, and J. Takala. 2018. PLOCTree: A fast, high-quality hardware BVH builder. *Proceedings of the ACM on Computer Graphics and Interactive Techniques* *1*  (2), 35:1–19.

[^33]: Viitanen, T., M. Koskela, P. Jääskeläinen, H. Kultala, and J. Takala. 2017. MergeTree: A fast hardware HLBVH constructor for animated ray tracing. *ACM Transactions on Graphics* *36*  (5), 169:1–14.

[^34]: Vinkler, M., V. Havran, J. Bittner, and J. Sochor. 2016. Parallel on-demand hierarchy construction on contemporary GPUs. *IEEE Transactions on Visualization and Computer Graphics* *22*  (7), 1886–98.

[^35]: Wald, I. 2011. Active thread compaction for GPU path tracing. *Proceedings of High Performance Graphics (HPG ’11)*, 51–58.

[^36]: Woop, S., J. Schmittler, and P. Slusallek. 2005. RPU: A programmable ray processing unit for realtime ray tracing. In *ACM SIGGRAPH 2005 Papers*, 434–44.

[^37]: Zhang, M., A. Alawneh, and T. G. Rogers. 2021. Judging a type by its pointer: Optimizing GPU virtual functions. *Proceedings of the 26th ACM International Conference on Architectural Support for Programming Languages and Operating Systems (ASPLOS 2021)*, 241–54.

[^38]: Zhou, K., Q. Hou, R. Wang, and B. Guo. 2008. Real-time kd-tree construction on graphics hardware. *ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia 2008)* *27*  (5), 126:1–11.

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[GPU渲染]]
- [[路径追踪]]

**同章节**:
- [[15 Wavefront Rendering on GPUs]]
- [[15.1 Mapping Path Tracing to the GPU]]
- [[15.2 Implementation Foundations]]
- [[15.3 Path Tracer Implementation]]
