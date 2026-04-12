---
title: "Exercises"
source: "https://pbr-book.org/4ed/Utilities/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
1. ② It is possible to use image pyramids and MIP mapping with images that have non-power-of-two resolutions—the details are explained by Guthe and Heckbert ([2005](https://pbr-book.org/4ed/Utilities/Further_Reading.html#cite:Guthe05)). Implementing this approach can save a substantial amount of memory: in the worst case, the resampling that pbrt 's [MIPMap](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#MIPMap) implementation performs can increase memory requirements by a factor of four. (Consider a texture that is resampled to be.) Implement this approach in pbrt, and compare the amount of memory used to store texture data for a variety of texture-heavy scenes.
> ② 可以对非 2 的幂次分辨率的图像使用图像金字塔和 MIP 映射——细节由 Guthe 和 Heckbert（[2005](https://pbr-book.org/4ed/Utilities/Further_Reading.html#cite:Guthe05)）解释。实现此方法可以节省大量内存：在最坏情况下，pbrt 的 [MIPMap](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#MIPMap) 实现执行的重采样可以将内存需求增加四倍。在 pbrt 中实现此方法，并比较各种纹理密集场景中用于存储纹理数据的内存量。
2. ② Improve the filtering algorithm used in the [Image::GeneratePyramid()](https://pbr-book.org/4ed/Utilities/Images.html#Image::GeneratePyramid) method to initialize the pyramid levels using the Lanczos filter instead of the box filter. How do the sphere test images in Figure [10.16](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#fig:texfilt-ewa-trilerp) change after your modifications? Do you see a difference in other scenes that use image textures?
> ② 改进 [Image::GeneratePyramid()](https://pbr-book.org/4ed/Utilities/Images.html#Image::GeneratePyramid) 方法中使用的过滤算法，使用 Lanczos 滤波器而非盒式滤波器来初始化金字塔层级。修改后图 [10.16](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#fig:texfilt-ewa-trilerp) 中的球体测试图像有何变化？在使用图像纹理的其他场景中你是否看到差异？
3. ② Try a few alternative implementations of the statistics system described in Section [B.7](https://pbr-book.org/4ed/Utilities/Statistics.html#sec:stats) to get a sense of the performance trade-offs with various approaches. You might try using atomic operations to update single counters that are shared across threads, or you might try using a mutex to allow safe updates to shared counters by multiple threads. Measure the performance compared to pbrt 's current implementation and discuss possible explanations for your results.
> ② 尝试第 [B.7](https://pbr-book.org/4ed/Utilities/Statistics.html#sec:stats) 节中描述的统计系统的几种替代实现，以了解各种方法的性能权衡。你可以尝试使用原子操作来更新跨线程共享的单个计数器，或者尝试使用互斥锁来允许多线程安全地更新共享计数器。将性能与 pbrt 当前实现进行比较，并讨论结果的可能解释。
4. ③ Generalize the statistics system (including the per-pixel statistics) so that it is also available in the GPU rendering path. You will likely want to pursue an approach based on atomic variables rather than the thread\_local approach that is used for the CPU. Measure the performance of your implementation and compare to the system before your changes. Is performance meaningfully affected?
> ③ 推广统计系统（包括逐像素统计），使其在 GPU 渲染路径中也可用。你可能需要采用基于原子变量的方法，而非 CPU 使用的 thread\_local 方法。测量你实现的性能并与修改前的系统进行比较。性能是否受到显著影响？