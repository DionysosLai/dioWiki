---
title: "Volume Scattering"
source: "https://pbr-book.org/4ed/Volume_Scattering"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 11 Volume Scattering

We have assumed so far that scenes are made up of collections of surfaces in a vacuum, which means that radiance is constant along rays between surfaces. However, there are many real-world situations where this assumption is inaccurate: fog and smoke attenuate and scatter light, and scattering from particles in the atmosphere makes the sky blue and sunsets red. This chapter introduces the mathematics that describe how light is affected as it passes through *participating media* —large numbers of very small particles distributed throughout a region of 3D space. These volume scattering models in computer graphics are based on the assumption that there are so many particles that scattering is best modeled as a probabilistic process rather than directly accounting for individual interactions with particles. Simulating the effect of participating media makes it possible to render images with atmospheric haze, beams of light through clouds, light passing through cloudy water, and subsurface scattering, where light exits a solid object at a different place than where it entered.

> 到目前为止，我们一直假设场景由真空中的表面集合组成，这意味着辐射亮度在表面之间的光线上是恒定的。然而，有许多现实世界的情况使这一假设不准确：雾和烟会衰减和散射光线，大气中粒子的散射使天空呈蓝色、日落呈红色。本章介绍了描述光在穿过*参与介质*时如何受到影响的数学——参与介质是分布在三维空间区域中的大量非常小的粒子。计算机图形学中的这些体积散射模型基于这样的假设：粒子数量如此之多，以至于散射最好建模为概率过程，而不是直接考虑与单个粒子的交互。模拟参与介质的效果使得渲染具有大气雾霾、穿过云层的光束、穿过浑浊水的光线以及次表面散射（光从与进入不同的位置离开固体物体）的图像成为可能。

This chapter first describes the basic physical processes that affect the radiance along rays passing through participating media, including the phase function, which characterizes the distribution of light scattered at a point in space. (It is the volumetric analog to the BSDF.) It then introduces transmittance, which describes the attenuation of light in participating media. Computing unbiased estimates of transmittance can be tricky, so we then discuss null scattering, a mathematical formalism that makes it easier to sample scattering integrals like the one that describes transmittance. Next, the [Medium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#Medium) interface is defined; it is used for representing the properties of participating media in a region of space. Medium implementations provide information about the scattering properties at points in their extent. This chapter does not cover techniques related to computing lighting and the effect of multiple scattering in volumetric media; the associated Monte Carlo integration algorithms and implementations of [Integrator](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Integrator) s that handle volumetric effects will be the topic of Chapter [14](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering.html#chap:volume-integration).

> 本章首先描述影响穿过参与介质的光线上辐射亮度的基本物理过程，包括相位函数，它表征了空间中某点处散射光的分布。（它是 BSDF 的体积类比。）然后引入透射率，描述参与介质中光的衰减。计算透射率的无偏估计可能很棘手，因此我们接着讨论零散射（null scattering），这是一种使采样散射积分（如描述透射率的积分）更容易的数学形式。接下来定义了 Medium 接口；它用于表示空间区域中参与介质的属性。Medium 实现提供其范围内各点的散射属性信息。本章不涉及与计算照明和体积介质中多次散射效果相关的技术；处理体积效果的相关蒙特卡洛积分算法和 Integrator 实现将是第 14 章的主题。