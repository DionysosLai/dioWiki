---
title: "Light Transport II: Volume Rendering"
source: "https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 14 Light Transport II: Volume Rendering

The abstractions for representing participating media that were introduced in Chapter [11](https://pbr-book.org/4ed/Volume_Scattering.html#chap:volume-scattering) describe how media scatter light but they do not provide the capability of simulating the global effects of light transport in a scene. The situation is similar to that with BSDFs: they describe local effects, but it was necessary to start to introduce integrators in Chapter [13](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection.html#chap:light-transport) that accounted for direct lighting and interreflection in order to render images. This chapter does the same for volumetric scattering.

> 第 11 章引入的用于表示参与介质的抽象描述了介质如何散射光，但它们不提供模拟场景中光传输全局效果的能力。情况类似于 BSDF：它们描述局部效果，但有必要在第 13 章开始引入考虑直接光照和互反射的积分器以渲染图像。本章对体积散射做了同样的事情。

We begin with the introduction of the equation of transfer, which generalizes the light transport equation to describe the equilibrium distribution of radiance in scenes with participating media. Like the transmittance equations in Section [11.2](https://pbr-book.org/4ed/Volume_Scattering/Transmittance.html#sec:transmittance), the equation of transfer has a null-scattering generalization that allows sampling of heterogeneous media for unbiased integration. We will also introduce a path integral formulation of it that generalizes the surface path integral from Section [13.1.4](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection/The_Light_Transport_Equation.html#sec:pathspace).

> 我们从引入传输方程开始，它推广了光传输方程以描述具有参与介质的场景中辐射亮度的平衡分布。与第 11.2 节中的透射率方程一样，传输方程有一个零散射推广，允许对非均匀介质进行采样以实现无偏积分。我们还将引入它的路径积分形式，推广第 13.1.4 节中的表面路径积分。

Following sections discuss implementations of solutions to the equation of transfer. Section [14.2](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Volume_Scattering_Integrators.html#sec:vol-light-transport) introduces two [Integrator](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Integrator) s that use Monte Carlo integration to solve the full equation of transfer, making it possible to render scenes with complex volumetric effects. Section [14.3](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Scattering_from_Layered_Materials.html#sec:scattering-layered) then describes the implementation of [LayeredBxDF](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Scattering_from_Layered_Materials.html#LayeredBxDF), which solves a 1D specialization of the equation of transfer to model scattering from layered materials at surfaces.

> 后续各节讨论传输方程解的实现。第 14.2 节引入了两个使用蒙特卡洛积分求解完整传输方程的 Integrator，使得渲染具有复杂体积效果的场景成为可能。第 14.3 节描述了 LayeredBxDF 的实现，它求解传输方程的一维特化以建模表面分层材质的散射。