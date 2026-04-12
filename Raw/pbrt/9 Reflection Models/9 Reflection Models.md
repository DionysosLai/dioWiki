---
title: "Reflection Models"
source: "https://pbr-book.org/4ed/Reflection_Models"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 9 Reflection Models

This chapter defines a set of classes for describing the way that light scatters at surfaces. Recall that in Section [4.3.1](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Surface_Reflection.html#sec:brdf) we introduced the bidirectional reflectance distribution function (BRDF) abstraction to describe light reflection at a surface, the bidirectional transmittance distribution function (BTDF) to describe transmission at a surface, and the bidirectional scattering distribution function (BSDF) to encompass both of these effects. In this chapter, we will start by defining a generic interface to these surface reflection and transmission functions.

> 本章定义了一组类来描述光在表面的散射方式。回顾第 4.3.1 节，我们引入了双向反射分布函数（BRDF）抽象来描述表面的光反射，双向透射分布函数（BTDF）来描述表面的透射，以及双向散射分布函数（BSDF）来涵盖这两种效应。在本章中，我们将首先定义这些表面反射和透射函数的通用接口。

Surface reflection models come from a number of sources:

> 表面反射模型来自多种来源：

- *Measured data:* Reflection distribution properties of many real-world surfaces have been measured in laboratories. Such data may be used directly in tabular form or to compute coefficients for a set of basis functions.

> - *测量数据：* 许多真实世界表面的反射分布特性已在实验室中测量。这些数据可以直接以表格形式使用，或用于计算一组基函数的系数。

- *Phenomenological models:* Equations that attempt to describe the qualitative properties of real-world surfaces can be remarkably effective at mimicking them. These types of BSDFs can be particularly easy to use, since they tend to have intuitive parameters that modify their behavior (e.g., "roughness").

> - *唯象模型：* 试图描述真实世界表面定性特性的方程在模仿它们方面可以非常有效。这类 BSDF 特别易于使用，因为它们通常具有直观的参数来修改其行为（例如"粗糙度"）。

- *Simulation:* Sometimes, low-level information is known about the composition of a surface. For example, we might know that a paint is comprised of colored particles of some average size suspended in a medium or that a particular fabric is comprised of two types of threads with known reflectance properties. In this case, a preprocess could simulate the behavior of light within the microstructure to fit an approximate BSDF. Alternatively, simulation could occur when rendering.

> - *模拟：* 有时我们知道表面组成的底层信息。例如，我们可能知道某种油漆由悬浮在介质中的某种平均大小的有色颗粒组成，或者某种特定织物由两种具有已知反射特性的线组成。在这种情况下，预处理可以模拟光在微观结构中的行为以拟合近似的 BSDF。或者，模拟也可以在渲染时进行。

- *Physical (wave) optics:* Some reflection models have been derived using a detailed model of light, treating it as a wave and computing the solution to Maxwell's equations to find how it scatters from a surface with known properties. They are mainly of use when the scene contains geometric detail at the micrometer level that makes wave-optical behavior readily apparent, such as with thin films, coatings, and periodic structures as found on digital optical data storage formats like CDs and DVDs.

> - *物理（波动）光学：* 一些反射模型使用光的详细模型推导而来，将光视为波并计算麦克斯韦方程组的解，以找出它如何从具有已知属性的表面散射。它们主要在场景包含微米级几何细节时使用，这些细节使波动光学行为显而易见，例如薄膜、涂层以及 CD 和 DVD 等数字光学数据存储格式上的周期性结构。

- *Geometric optics:* As with simulation approaches, if the surface's low-level scattering and geometric properties are known, then closed-form reflection models can sometimes be derived directly from these descriptions. Geometric optics makes modeling light's interaction with the surface more tractable, since complex wave effects like diffraction can be ignored.

> - *几何光学：* 与模拟方法一样，如果表面的底层散射和几何属性已知，那么有时可以直接从这些描述推导出封闭形式的反射模型。几何光学使得建模光与表面的交互更加容易处理，因为可以忽略衍射等复杂的波动效应。

This chapter discusses the implementation of a number of such models along with the associated theory. See also Section [14.3](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering/Scattering_from_Layered_Materials.html#sec:scattering-layered), which introduces a reflection model based on the simulation of light scattering in layered materials. The "Further Reading" section at the end of this chapter gives pointers to a wide variety of additional reflection models.

> 本章讨论了若干此类模型的实现及相关理论。另请参见第 14.3 节，该节介绍了一种基于分层材质中光散射模拟的反射模型。本章末尾的"延伸阅读"部分提供了指向各种其他反射模型的参考。

An important component of surface appearance is the spatial variation of reflection and transmission properties over the surface. The texture and material classes of Chapter [10](https://pbr-book.org/4ed/Textures_and_Materials.html#chap:texture) will address that problem, while the abstractions presented here will only consider scattering at a single point on a surface. Further, BRDFs and BTDFs explicitly only model scattering from light that enters and exits a surface at a single point. For surfaces that exhibit meaningful subsurface light transport, a more complete simulation of light scattering inside the material is necessary—for example, by applying the volumetric light transport algorithms of Chapter [14](https://pbr-book.org/4ed/Light_Transport_II_Volume_Rendering.html#chap:volume-integration).

> 表面外观的一个重要组成部分是反射和透射属性在表面上的空间变化。第 10 章的纹理和材质类将解决这个问题，而这里呈现的抽象只考虑表面上单个点处的散射。此外，BRDF 和 BTDF 明确只建模在表面单个点进入和离开的光的散射。对于表现出显著次表面光传输的表面，需要对材质内部的光散射进行更完整的模拟——例如，通过应用第 14 章的体积光传输算法。

#### Basic Terminology

> #### 基本术语

We now introduce basic terminology for describing reflection from surfaces. To compare the resulting visual appearance, we will classify reflection into the following four broad categories: *diffuse*, *glossy specular*, *perfect specular*, and *retroreflective* (Figure [9.1](#fig:reflection-types)). Most real surfaces exhibit a mixture of these four behaviors. Diffuse surfaces scatter light equally in all directions. Although a perfectly diffuse surface is not physically realizable, examples of near-diffuse surfaces include dull chalkboards and matte paint. Glossy specular surfaces such as plastic or high-gloss paint scatter light preferentially in a set of reflected directions—they show blurry reflections of other objects. Perfect specular surfaces scatter incident light in a single outgoing direction. Mirrors and glass are examples of perfect specular surfaces. Finally, retroreflective surfaces like velvet or the Earth's moon scatter light primarily back along the incident direction. Images throughout this chapter will show the differences between these various behaviors in rendered scenes.

> 我们现在介绍描述表面反射的基本术语。为了比较产生的视觉外观，我们将反射分为以下四大类：*漫反射*、*光泽镜面反射*、*完美镜面反射*和*逆反射*（图 9.1）。大多数真实表面表现出这四种行为的混合。漫反射表面在所有方向上均匀散射光线。虽然完全漫反射的表面在物理上不可实现，但接近漫反射的表面示例包括暗淡的黑板和哑光油漆。光泽镜面反射表面如塑料或高光泽油漆优先在一组反射方向上散射光线——它们显示出其他物体的模糊反射。完美镜面反射表面将入射光散射到单一出射方向。镜子和玻璃是完美镜面反射表面的例子。最后，逆反射表面如天鹅绒或地球的月亮主要沿入射方向散射光线。本章中的图片将展示渲染场景中这些不同行为之间的差异。

[![](https://pbr-book.org/4ed/Reflection_Models%2Fpha09f01.svg)](https://pbr-book.org/4ed/Reflection_Models%2Fpha09f01.svg)

Figure 9.1: Reflection from a surface can be generally categorized by the distribution of reflected light from an incident direction (heavy lines): (a) diffuse, (b) glossy specular, (c) nearly perfect specular, and (d) retroreflective distributions.

> 图 9.1：表面反射通常可以通过从入射方向（粗线）反射光的分布来分类：(a) 漫反射，(b) 光泽镜面反射，(c) 近完美镜面反射，和 (d) 逆反射分布。

Given a particular category of reflection, the reflectance distribution function may be *isotropic* or *anisotropic*. With an isotropic material, if you choose a point on the surface and rotate it around its normal axis at that point, the distribution of light reflected at that point does not change. Diffuse materials like paper or wall paint are usually isotropic due to the directionally random arrangement of wood fibers or paint particles.

> 给定特定的反射类别，反射分布函数可能是*各向同性的*或*各向异性的*。对于各向同性材质，如果选择表面上的一个点并围绕该点的法线轴旋转，该点反射光的分布不会改变。纸张或墙面油漆等漫反射材质通常是各向同性的，这是由于木纤维或油漆颗粒的方向随机排列。

In contrast, anisotropic materials reflect different amounts of light as you rotate them in this way. Examples of anisotropic materials include hair and many types of cloth. Industrial processes such as milling, rolling, extrusion, and 3D printing also tend to produce highly anisotropic surfaces, an extreme example being brushed metal.

> 相比之下，各向异性材质在这样旋转时反射不同量的光。各向异性材质的例子包括头发和许多类型的布料。铣削、轧制、挤压和 3D 打印等工业工艺也倾向于产生高度各向异性的表面，一个极端的例子是拉丝金属。

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[反射模型]]
- [[BSDF]]

**同章节**:
- [[9.1 BSDF Representation]]
- [[9.2 Diffuse Reflection]]
- [[9.3 Specular Reflection and Transmission]]
- [[9.4 Conductor BRDF]]
- [[9.5 Dielectric BSDF]]
- [[9.6 Roughness Using Microfacet Theory]]
- [[9.7 Rough Dielectric BSDF]]
- [[9.8 Measured BSDFs]]
- [[9.9 Scattering from Hair]]
