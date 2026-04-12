---
title: "Textures and Materials"
source: "https://pbr-book.org/4ed/Textures_and_Materials"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 10 Textures and Materials

The BRDFs and BTDFs introduced in the previous chapter address only part of the problem of describing how a surface scatters light. Although they describe how light is scattered at a particular point on a surface, the renderer needs to determine *which* BRDFs and BTDFs are present at a point on a surface and what their parameters are. In this chapter, we describe a procedural shading mechanism that addresses this issue.

> 前一章引入的 BRDF 和 BTDF 只解决了描述表面如何散射光的部分问题。虽然它们描述了光在表面特定点处如何散射，但渲染器需要确定表面某点处存在*哪些* BRDF 和 BTDF 以及它们的参数是什么。在本章中，我们描述了一种解决此问题的程序化着色机制。

There are two components to pbrt 's approach: textures, which describe the spatial variation of some scalar or spectral value over a surface, and materials, which evaluate textures at points on surfaces in order to determine the parameters for their associated BSDFs. Separating the pattern generation responsibilities of textures from the implementations of reflection models via materials makes it easy to combine them in arbitrary ways, thereby making it easier to create a wide variety of appearances.

> pbrt 的方法有两个组成部分：纹理描述某个标量或光谱值在表面上的空间变化，而材质在表面上的点处评估纹理以确定其关联 BSDF 的参数。将纹理的图案生成职责与通过材质实现的反射模型分离，使得以任意方式组合它们变得容易，从而更容易创造各种各样的外观。

In pbrt, a *texture* is a fairly general concept: it is a function that maps points in some domain (e.g., a surface's parametric space or object space) to values in some other domain (e.g., spectra or the real numbers). A variety of implementations of texture classes are available in the system. For example, pbrt has textures that represent zero-dimensional functions that return a constant in order to accommodate surfaces that have the same parameter value everywhere. Image map textures are two-dimensional functions that use a 2D array of pixel values to compute texture values at particular points (they are described in Section [10.4](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#sec:texture-mapping)). There are even texture functions that compute values based on the values computed by other texture functions.

> 在 pbrt 中，*纹理*是一个相当通用的概念：它是一个将某个域（例如表面的参数空间或对象空间）中的点映射到另一个域（例如光谱或实数）中的值的函数。系统中有多种纹理类实现。例如，pbrt 有代表零维函数的纹理，它们返回一个常量以适应在所有地方具有相同参数值的表面。图像贴图纹理是使用像素值的二维数组在特定点处计算纹理值的二维函数（在第 10.4 节中描述）。甚至还有基于其他纹理函数计算值的纹理函数。

Textures may be a source of high-frequency variation in the final image. Figure [10.1](#fig:texture-aliasing-example) shows an image with severe aliasing due to a texture. Although the visual impact of this aliasing can be reduced with the nonuniform sampling techniques from Chapter [8](https://pbr-book.org/4ed/Sampling_and_Reconstruction.html#chap:sampling-reconstruction), a better solution to this problem is to implement texture functions that adjust their frequency content based on the rate at which they are being sampled. For many texture functions, computing a reasonable approximation to the frequency content and antialiasing in this manner are not too difficult and are substantially more efficient than reducing aliasing by increasing the image sampling rate.

> 纹理可能是最终图像中高频变化的来源。图 10.1 显示了由于纹理导致严重走样的图像。虽然可以用第 8 章的非均匀采样技术来减少此走样的视觉影响，但更好的解决方案是实现根据采样率调整其频率内容的纹理函数。对于许多纹理函数，以这种方式计算频率内容的合理近似并进行反走样并不太困难，而且比通过提高图像采样率来减少走样要高效得多。

![](https://pbr-book.org/4ed/Textures_and_Materials%2Fsphere-point-vs-ewa.png)

Figure 10.1: Texture Aliasing. Both spheres have the same grid texture applied and each pixel is sampled at its center. No antialiasing is performed on the left sphere; because the texture has higher frequencies than the pixel sampling rate, severe aliasing results. On the right sphere, the texture function has taken into account the image sampling rate to prefilter its function and remove high-frequency detail, giving an antialiased result even with a single sample in each pixel.

> 图 10.1：纹理走样。两个球体应用了相同的网格纹理，每个像素在其中心采样。左球未进行反走样；因为纹理的频率高于像素采样率，导致严重走样。右球的纹理函数考虑了图像采样率来预滤波其函数并去除高频细节，即使每个像素只有一个样本也能给出反走样的结果。

The first section of this chapter will discuss the problem of texture aliasing and general approaches to solving it. We will then describe the basic texture interface and illustrate its use with a variety of texture functions. After the textures have been defined, the last section, [10.5](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#sec:material-interface), introduces the Material interface and a number of implementations.

> 本章第一节将讨论纹理走样问题及解决它的一般方法。然后我们将描述基本的纹理接口，并通过各种纹理函数说明其用法。在纹理定义之后，最后一节 10.5 介绍了 Material 接口及若干实现。