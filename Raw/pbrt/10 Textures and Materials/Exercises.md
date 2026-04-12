---
title: "Exercises"
source: "https://pbr-book.org/4ed/Textures_and_Materials/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
1. ② Read the papers by Manson and Schaefer ([2013](https://pbr-book.org/4ed/Textures_and_Materials/Further_Reading.html#cite:Manson2013), [2014](https://pbr-book.org/4ed/Textures_and_Materials/Further_Reading.html#cite:Manson2014)) on approximating high-quality filters with MIP maps and a small number of bilinear samples. Add an option to use their method for texture filtering in place of the EWA implementation currently in pbrt. Compare image quality for a number of scenes that use textures. How does running time compare? You may also find it beneficial to use a profiler to compare the amount of time it takes to run texture filtering code for each of the two approaches.

> 1. ② 阅读 Manson 和 Schaefer (2013, 2014) 关于用 MIP 映射和少量双线性样本近似高质量滤波器的论文。添加一个选项，用他们的方法代替 pbrt 中当前的 EWA 实现进行纹理滤波。比较使用纹理的多个场景的图像质量。运行时间如何比较？使用分析器比较两种方法的纹理滤波代码运行时间也可能有益。

2. ② An additional advantage of properly antialiased image map lookups is that they improve cache performance. Consider, for example, the situation of undersampling a high-resolution image map: nearby samples on the screen will access widely separated parts of the image map, such that there is low probability that texels fetched from main memory for one texture lookup will already be in the cache for texture lookups at adjacent pixel samples. Modify pbrt so that it always does image texture lookups from the finest level of the MIPMap, being careful to ensure that the same number of texels are still being accessed. How does performance change? What do cache-profiling tools report about the overall change in effectiveness of the CPU cache?

> 2. ② 正确反走样的图像贴图查找的一个额外优势是它们改善了缓存性能。例如，考虑对高分辨率图像贴图欠采样的情况：屏幕上相邻的样本将访问图像贴图中相距很远的部分，因此为一次纹理查找从主存取出的纹素已在相邻像素样本的纹理查找缓存中的概率很低。修改 pbrt 使其始终从 MIPMap 的最精细层进行图像纹理查找，同时确保仍访问相同数量的纹素。性能如何变化？缓存分析工具报告的 CPU 缓存有效性的整体变化是什么？

3. ② Read Worley's paper that describes a noise function with substantially different visual characteristics than Perlin noise ([Worley 1996](https://pbr-book.org/4ed/Textures_and_Materials/Further_Reading.html#cite:Worley96)). Implement this cellular noise function, and add [Texture](https://pbr-book.org/4ed/Textures_and_Materials/Texture_Interface_and_Basic_Textures.html#Texture) s to pbrt that are based on it.

> 3. ② 阅读 Worley 的论文，该论文描述了一种与 Perlin 噪声具有显著不同视觉特征的噪声函数 (Worley 1996)。实现这个细胞噪声函数，并向 pbrt 添加基于它的 Texture。

4. ③ Read some of the papers on filtering bump maps referenced in the "Further Reading" section of this chapter, choose one of the techniques described there, and implement it in pbrt. Show the visual artifacts from bump map aliasing without the technique you implement, as well as examples of how well your implementation addresses them.

> 4. ③ 阅读本章"延伸阅读"部分引用的一些关于滤波凹凸贴图的论文，选择其中描述的一种技术，并在 pbrt 中实现它。展示没有你实现的技术时凹凸贴图走样的视觉伪影，以及你的实现如何解决它们的示例。

5. ③ Modify pbrt to support a shading language to allow user-written programs to compute texture values. Unless you are also interested in writing your own compiler, *OSL* (Gritz et al. [2010](https://pbr-book.org/4ed/Textures_and_Materials/Further_Reading.html#cite:Gritz2010)) is a good choice.

> 5. ③ 修改 pbrt 以支持着色语言，允许用户编写的程序计算纹理值。除非你也有兴趣编写自己的编译器，否则 *OSL* (Gritz et al. 2010) 是一个好选择。