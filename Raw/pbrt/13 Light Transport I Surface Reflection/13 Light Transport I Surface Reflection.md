---
title: "Light Transport I: Surface Reflection"
source: "https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 13 Light Transport I: Surface Reflection

This chapter brings together the ray-tracing algorithms, radiometric concepts, and Monte Carlo sampling algorithms of the previous chapters to implement two different integrators that compute scattered radiance from surfaces in the scene. These integrators are much more effective than the [RandomWalkIntegrator](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#RandomWalkIntegrator) from the first chapter; with them, some scenes are rendered with hundreds of times lower error.

> 本章将前面各章的光线追踪算法、辐射度量概念和蒙特卡洛采样算法结合在一起，实现了两种不同的积分器来计算场景中表面的散射辐射亮度。这些积分器比第一章的 RandomWalkIntegrator 有效得多；使用它们，某些场景的渲染误差降低了数百倍。

We start by deriving the light transport equation, which was first introduced in Section [1.2.6](https://pbr-book.org/4ed/Introduction/Photorealistic_Rendering_and_the_Ray-Tracing_Algorithm.html#sec:indirect-light-transport-basics). We can then formally introduce the path-tracing algorithm, which applies Monte Carlo integration to solve that equation. We will then describe the implementation of the [SimplePathIntegrator](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/A_Simple_Path_Tracer.html#SimplePathIntegrator), which provides a pared-down implementation of path tracing that is useful for understanding the basic algorithm and for debugging sampling algorithms. The chapter concludes with the [PathIntegrator](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/A_Better_Path_Tracer.html#PathIntegrator), which is a more complete path tracing implementation.

> 我们从推导光传输方程开始，该方程首先在第 1.2.6 节中引入。然后我们可以正式介绍路径追踪算法，它应用蒙特卡洛积分来求解该方程。接着我们将描述 SimplePathIntegrator 的实现，它提供了路径追踪的精简实现，对于理解基本算法和调试采样算法很有用。本章以 PathIntegrator 结束，它是更完整的路径追踪实现。

Both of these integrators find light-carrying paths starting from the camera, accounting for scattering from shapes' surfaces. Chapter [14](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering.html#chap:volume-integration) will extend path tracing to include the effects of participating media. (The online edition of this book also includes a chapter that describes bidirectional methods for constructing light-carrying paths starting both from the camera and from light sources.)

> 这两个积分器都从相机出发寻找携带光的路径，考虑形状表面的散射。第 14 章将扩展路径追踪以包括参与介质的效果。（本书的在线版本还包括一章描述从相机和光源两端构建携带光的路径的双向方法。）