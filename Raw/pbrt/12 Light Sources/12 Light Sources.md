---
title: "Light Sources"
source: "https://pbr-book.org/4ed/Light_Sources"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 12 Light Sources

In order for objects in a scene to be visible, there must be a source of illumination so that some light is reflected from them to the camera sensor. To that end, this chapter first presents the [Light](https://pbr-book.org/4ed/Light_Sources/Light_Interface.html#Light) interface, which allows specification of a variety of types of light sources. (Before reading this chapter, you may wish to review Section [4.4](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Light_Emission.html#sec:emission), which describes the physical processes underlying light emission.)

> 为了使场景中的物体可见，必须有照明光源，使得一些光线从物体反射到相机传感器。为此，本章首先介绍了 Light 接口，它允许指定各种类型的光源。（在阅读本章之前，你可能希望复习第 4.4 节，该节描述了光发射的物理过程。）

The implementations of a number of useful light sources follow. Because the implementations of different types of lights are all hidden behind a carefully designed interface, the light transport routines in Chapters [13](https://pbr-book.org/4ed/Light_Transport_I_Surface_Reflection.html#chap:light-transport) through [15](https://pbr-book.org/4ed/Wavefront_Rendering_on_GPUs.html#chap:gpu) can generally operate without knowing which particular types of lights are in the scene, similar to how acceleration structures can hold collections of different types of primitives without needing to know the details of their actual representations.

> 接下来是若干有用光源的实现。因为不同类型光源的实现都隐藏在精心设计的接口之后，第 13 章到第 15 章的光传输例程通常可以在不知道场景中具体有哪些类型光源的情况下运行，类似于加速结构可以容纳不同类型图元的集合而无需知道其实际表示的细节。

A wide variety of light source models are introduced in this chapter, although the variety is slightly limited by pbrt 's physically based design. Many non-physical light source models have been developed for computer graphics, incorporating control over properties like the rate at which the light falls off with distance, which objects are illuminated by the light, which objects cast shadows from the light, and so on. These sorts of controls are incompatible with physically based light transport algorithms and thus cannot be provided in the models here.

> 本章引入了各种各样的光源模型，尽管由于 pbrt 基于物理的设计，种类略有限制。计算机图形学已经开发了许多非物理的光源模型，它们包含了对光随距离衰减速率、哪些物体被光照亮、哪些物体从光投射阴影等属性的控制。这类控制与基于物理的光传输算法不兼容，因此无法在此处的模型中提供。

As an example of the problems such lighting controls pose, consider a light that does not cast shadows: the total energy arriving at surfaces in the scene increases without bound as more surfaces are added. Consider a series of concentric shells of spheres around such a light; if occlusion is ignored, each added shell increases the total received energy. This directly violates the principle that the total energy arriving at surfaces illuminated by the light cannot be greater than the total energy emitted by the light.

> 作为此类光照控制带来问题的示例，考虑一个不投射阴影的光源：随着更多表面被添加，到达场景中表面的总能量无限增加。考虑围绕此类光源的一系列同心球壳；如果忽略遮挡，每个添加的球壳都会增加总接收能量。这直接违反了被光照亮的表面接收的总能量不能大于光源发射的总能量的原则。

In scenes with many lights, it is impractical to account for the illumination from all of them at each point being shaded. Fortunately, this issue is yet another that can be handled stochastically. Given a suitable weighting factor, an unbiased estimate of the effect of illumination from all the lights can be computed by considering just a few of them, or even just one. The last section of this chapter therefore introduces the [LightSampler](https://pbr-book.org/4ed/Light_Sources/Light_Sampling.html#LightSampler), which defines an interface for choosing such light sources as well as a number of implementations of it.

> 在有许多光源的场景中，在每个着色点考虑所有光源的照明是不切实际的。幸运的是，这是另一个可以随机处理的问题。给定合适的权重因子，只考虑少数几个甚至一个光源就可以计算所有光源照明效果的无偏估计。因此，本章最后一节引入了 LightSampler，它定义了选择此类光源的接口以及若干实现。

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[光源]]

**同章节**:
- [[12.1 Light Interface]]
- [[12.2 Point Lights]]
- [[12.3 Distant Lights]]
- [[12.4 Area Lights]]
- [[12.5 Infinite Area Lights]]
- [[12.6 Light Sampling]]
