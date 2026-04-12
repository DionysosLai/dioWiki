---
title: "Wavefront Rendering on GPUs"
source: "https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 15 Wavefront Rendering on GPUs

One of the major changes in pbrt for this edition of the book is the addition of support for rendering on GPUs as well as on CPUs. Between the substantial computational capabilities that GPUs offer and the recent availability of custom hardware units for efficient ray intersection calculations, the GPU is a compelling target for ray tracing. For example, the image in Figure [15.1](#fig:cpu-gpu-example-scene) takes 318.6 seconds to render with pbrt on a 2020-era high-end GPU at resolution with 2048 samples per pixel. On an 8-core CPU, it takes 11,983 seconds to render with the same settings—over 37 times longer. Even on a high-end 32-core CPU, it takes 2,669 seconds to render (still over 8 times longer).

> pbrt 在本版书中的重大变化之一是增加了对 GPU 和 CPU 渲染的支持。凭借 GPU 提供的强大计算能力以及最近可用的用于高效光线求交计算的定制硬件单元，GPU 是光线追踪的理想目标。例如，图 15.1 中的图像在 2020 年代高端 GPU 上使用 pbrt 以 2048 每像素样本渲染需要 318.6 秒。在 8 核 CPU 上，以相同设置渲染需要 11,983 秒——超过 37 倍。即使在高端 32 核 CPU 上，渲染也需要 2,669 秒（仍然超过 8 倍）。

![](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs%2Fkroken-cpu-gpu.png)

Figure 15.1: Scene Used for CPU versus GPU Ray Tracing Performance Comparison. *(Scene courtesy of Angelo Ferretti.)*

> 图 15.1：用于 CPU 与 GPU 光线追踪性能比较的场景。*（场景由 Angelo Ferretti 提供。）*

pbrt 's GPU rendering path offers only a single integration algorithm: volumetric path tracing, following the algorithms used in the CPU-based [VolPathIntegrator](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Volume_Scattering_Integrators.html#VolPathIntegrator) described in Section [14.2.3](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Volume_Scattering_Integrators.html#sec:volumetric-path-tracing). It otherwise supports all of pbrt 's functionality, using the same classes and functions that have been presented in the preceding 14 chapters. This chapter will therefore not introduce any new rendering algorithms but instead will focus on topics like parallelism and data layout in memory that are necessary to achieve good performance on GPUs.

> pbrt 的 GPU 渲染路径只提供一种积分算法：体积路径追踪，遵循第 14.2.3 节描述的基于 CPU 的 VolPathIntegrator 使用的算法。它在其他方面支持 pbrt 的所有功能，使用前 14 章中介绍的相同类和函数。因此，本章不会引入任何新的渲染算法，而是专注于并行性和内存中的数据布局等在 GPU 上获得良好性能所必需的主题。

The integrator described in this chapter, [WavefrontPathIntegrator](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs/Path_Tracer_Implementation.html#WavefrontPathIntegrator), is structured using a *wavefront* architecture—effectively, many rays are processed simultaneously, with rendering work organized in queues that collect related tasks to be processed together. ("Wavefront" in this context will be defined more precisely in Section [15.1.2](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs/Mapping_Path_Tracing_to_the_GPU.html#sec:gpu-structuring-rendering-computation).)

> 本章描述的积分器 WavefrontPathIntegrator 采用*波前*（wavefront）架构——实际上同时处理许多光线，渲染工作组织在收集相关任务以一起处理的队列中。（此上下文中的"波前"将在第 15.1.2 节中更精确地定义。）

Some of the code discussed in this chapter makes more extensive use of advanced C++ features than we have generally used in previous chapters. While we have tried not to use such features unnecessarily, we will see that in some cases they make it possible to generate highly specialized code that runs much more efficiently than if their capabilities are not used. We had previously sidestepped many low-level optimizations due to their comparatively small impact on CPUs. Such implementation-level decisions can, however, change rendering performance by orders of magnitude when targeting GPUs.

> 本章讨论的一些代码比我们在前面各章中通常使用的更广泛地使用了高级 C++ 特性。虽然我们尽量不在不必要时使用这些特性，但我们将看到在某些情况下它们使得生成高度特化的代码成为可能，这些代码比不使用其功能时运行效率高得多。我们之前回避了许多底层优化，因为它们在 CPU 上的影响相对较小。然而，当针对 GPU 时，这类实现级别的决策可能使渲染性能改变数个数量级。

The [WavefrontPathIntegrator](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs/Path_Tracer_Implementation.html#WavefrontPathIntegrator) imposes three requirements on a GPU platform:

> WavefrontPathIntegrator 对 GPU 平台提出三项要求：

1. It must support a *unified address space*, where the CPU and GPU can both access the GPU's memory, using pointers that are consistent on both types of processor. This capability is integral to being able to parse the scene description and initialize the scene representation on the CPU, including initializing pointer-based data structures there, before the same data structures are then used in code that runs on the GPU.

> 1. 它必须支持*统一地址空间*，CPU 和 GPU 都可以使用在两种处理器上一致的指针访问 GPU 的内存。此功能对于能够在 CPU 上解析场景描述并初始化场景表示至关重要，包括在那里初始化基于指针的数据结构，然后在 GPU 上运行的代码中使用相同的数据结构。

2. The GPU compilation infrastructure must be compatible with C++17, the language that the rest of pbrt is implemented in. This makes it possible to use the same class and function implementations on both types of processors.

> 2. GPU 编译基础设施必须与 C++17 兼容，这是 pbrt 其余部分的实现语言。这使得在两种处理器上使用相同的类和函数实现成为可能。

3. The GPU must have support for ray tracing, either in hardware or in vendor-supplied software. (pbrt 's existing acceleration structures would not be efficient on the GPU in their present form.)

> 3. GPU 必须支持光线追踪，无论是在硬件中还是在供应商提供的软件中。（pbrt 现有的加速结构以其当前形式在 GPU 上不会高效。）

The attentive reader will note that CPUs themselves fulfill all of those requirements, the third potentially via pbrt 's acceleration structures from Chapter [7](https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration.html#chap:acceleration). Therefore, pbrt makes it possible to execute the [WavefrontPathIntegrator](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs/Path_Tracer_Implementation.html#WavefrontPathIntegrator) on CPUs as well; it is used if the –wavefront command-line option is provided. However, the wavefront organization is usually not a good fit for CPUs and performance is almost always worse than if the [VolPathIntegrator](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Volume_Scattering_Integrators.html#VolPathIntegrator) is used instead. Nonetheless, the CPU wavefront path is useful for debugging and testing the [WavefrontPathIntegrator](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs/Path_Tracer_Implementation.html#WavefrontPathIntegrator) implementation on systems that do not have suitable GPUs.

> 细心的读者会注意到 CPU 本身满足所有这些要求，第三个要求可能通过第 7 章的 pbrt 加速结构来满足。因此，pbrt 也可以在 CPU 上执行 WavefrontPathIntegrator；如果提供了 –wavefront 命令行选项就会使用它。然而，波前组织通常不适合 CPU，性能几乎总是比使用 VolPathIntegrator 差。尽管如此，CPU 波前路径对于在没有合适 GPU 的系统上调试和测试 WavefrontPathIntegrator 实现很有用。

At this writing, the only GPUs that provide all three of these capabilities are based on NVIDIA's CUDA platform, so NVIDIA's GPUs are the only ones that pbrt currently supports. We hope that it will be possible to support others in the future. Around two thousand lines of platform-specific code are required to handle low-level details like allocating unified memory, launching work on the GPU, and performing ray intersections on the GPU. As usual, we will not include platform-specific code in the book, but see the [gpu/](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/gpu/) directory in the pbrt source code distribution for its implementation.

> 在撰写本文时，唯一提供这三种能力的 GPU 基于 NVIDIA 的 CUDA 平台，因此 NVIDIA 的 GPU 是 pbrt 目前唯一支持的。我们希望未来能够支持其他 GPU。大约需要两千行平台特定代码来处理底层细节，如分配统一内存、在 GPU 上启动工作以及在 GPU 上执行光线求交。像往常一样，我们不会在书中包含平台特定代码，但请参见 pbrt 源代码分发中的 gpu/ 目录了解其实现。