---
title: "Exercises"
source: "https://pbr-book.org/4ed/Introduction/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## Exercise

> ## 练习

1. ① A good way to gain an understanding of pbrt is to follow the process of computing the radiance value for a single ray in a debugger. Build a version of pbrt with debugging symbols and set up your debugger to run pbrt with a not-too-complex scene. Set breakpoints in the [ImageTileIntegrator::Render()](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#ImageTileIntegrator::Render) method and trace through the process of how a ray is generated, how its radiance value is computed, and how its contribution is added to the image. The first time you do this, you may want to specify that only a single thread of execution should be used by providing –nthreads 1 as command-line arguments to pbrt; doing so ensures that all computation is done in the main processing thread, which may make it easier to understand what is going on, depending on how easy your debugger makes it to step through the program when it is running multiple threads. As you gain more understanding about the details of the system later in the book, repeat this process and trace through particular parts of the system more carefully.

> 1. ① 理解 pbrt 的一个好方法是在调试器中跟踪单条光线的辐射亮度值的计算过程。用调试符号编译 pbrt 的一个版本，并设置调试器以一个不太复杂的场景运行 pbrt。在 [ImageTileIntegrator::Render()](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#ImageTileIntegrator::Render) 方法中设置断点，追踪光线的生成过程、辐射亮度值的计算方式，以及其贡献如何被添加到图像中。第一次这样做时，你可能需要通过向 pbrt 提供 `--nthreads 1` 命令行参数来指定仅使用单线程执行；这样可以确保所有计算都在主处理线程中完成，这可能使你更容易理解正在发生的事情，具体取决于调试器在程序运行多线程时单步执行的便利程度。随着你在本书后续内容中对系统细节有了更深入的理解，重复这个过程并更仔细地追踪系统的特定部分。
