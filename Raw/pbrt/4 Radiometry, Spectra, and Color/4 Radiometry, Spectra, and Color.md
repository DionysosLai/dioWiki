---
title: "Radiometry, Spectra, and Color"
source: "https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 4 Radiometry, Spectra, and Color

To precisely describe how light is represented and sampled to compute images, we must first establish some background in *radiometry*—the study of the propagation of electromagnetic radiation in an environment. In this chapter, we will first introduce four key quantities that describe electromagnetic radiation: flux, intensity, irradiance, and radiance.

> 为了精确描述光如何被表示和采样以计算图像，我们必须首先建立一些*辐射度量学*的背景——研究电磁辐射在环境中传播的学科。在本章中，我们将首先介绍描述电磁辐射的四个关键量：通量、强度、辐照度和辐射亮度。

These radiometric quantities generally vary as a function of wavelength. The variation of each is described by its *spectral distribution*—a distribution function that gives the amount of light at each wavelength. (We will interchangeably use *spectrum* to describe spectral distributions, and *spectra* for a plurality of them.) Of particular interest in rendering are the wavelengths of electromagnetic radiation between approximately 380 nm and 780 nm, which account for light visible to humans. A variety of classes that are used to represent spectral distributions in pbrt are defined in Section 4.5.

> 这些辐射度量量通常随波长变化。每个量的变化由其*光谱分布*描述——一个给出每个波长处光量的分布函数。（我们将交替使用 *spectrum* 来描述光谱分布，*spectra* 表示复数形式。）在渲染中特别关注的是大约 380 nm 到 780 nm 之间的电磁辐射波长，这些波长对应人眼可见光。pbrt 中用于表示光谱分布的各种类定义在第 4.5 节中。

While spectral distributions are a purely physical concept, color is related to how humans perceive spectra. The lower wavelengths of light are said to be bluish colors, the middle wavelengths greens, and the upper wavelengths reds. It is important to have accurate models of color for two reasons: first, display devices like monitors expect colors rather than spectra to describe pixel values, so accurately converting spectra to appropriate colors is important for displaying rendered images. Second, emission and reflection properties of objects in scenes are often specified using colors; these colors must be converted into spectra for use in rendering. Section 4.6, at the end of this chapter, describes the properties of color in more detail and includes implementations of pbrt's color-related functionality.

> 虽然光谱分布是纯粹的物理概念，但颜色与人类如何感知光谱有关。较低波长的光被认为是偏蓝色的，中间波长是绿色的，较高波长是红色的。准确的颜色模型很重要，原因有二：首先，显示设备（如显示器）期望用颜色而非光谱来描述像素值，因此准确地将光谱转换为适当的颜色对于显示渲染图像很重要。其次，场景中物体的发射和反射属性通常使用颜色指定；这些颜色必须转换为光谱以用于渲染。本章末尾的第 4.6 节更详细地描述了颜色的属性，并包含了 pbrt 颜色相关功能的实现。
