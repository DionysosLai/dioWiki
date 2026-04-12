---
title: "Cameras and Film"
source: "https://pbr-book.org/4ed/Cameras_and_Film"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 5 Cameras and Film

In Chapter 1, we described the pinhole camera model that is commonly used in computer graphics. This model is easy to describe and simulate, but it neglects important effects that physical lenses have on light passing through them. For example, everything rendered with a pinhole camera is in sharp focus—a state of affairs not possible with real lens systems. Such images often look computer generated for their perfection. More generally, the distribution of radiance leaving a lens system is quite different from the distribution entering it; modeling this effect of lenses is important for accurately simulating the radiometry of image formation.

> 在第 1 章中，我们描述了计算机图形学中常用的针孔相机模型。该模型易于描述和模拟，但忽略了物理镜头对通过其中的光线产生的重要影响。例如，针孔相机渲染出的所有内容都是完全对焦的——这在真实镜头系统中是不可能的。这类图像由于过于完美而往往看起来像是计算机生成的。更一般地说，离开透镜系统的辐射分布与进入它的辐射分布有很大不同；建模透镜的这种效应对于准确模拟成像的辐射度量非常重要。

Camera lens systems introduce various aberrations that affect the images that they form; for example, *vignetting* causes a darkening toward the edges of images due to less light making it through to the edges of the film or sensor than to the center. Lenses can also cause *pincushion* or *barrel* distortion, which causes straight lines to be imaged as curves. Although lens designers work to minimize aberrations in their designs, they can still have a meaningful effect on images.

> 相机镜头系统会引入各种影响所成图像的像差；例如，*暗角*（vignetting）会导致图像边缘变暗，因为到达胶片或传感器边缘的光线比到达中心的少。透镜还可能导致*枕形畸变*或*桶形畸变*，使直线被成像为曲线。尽管镜头设计师努力在设计中最小化像差，它们仍然会对图像产生显著影响。

This chapter starts with a description of the Camera interface, after which we present a few implementations, starting with ideal pinhole models.

> 本章从描述 Camera 接口开始，之后我们将介绍几种实现，从理想的针孔模型开始。

After light has been captured by a camera, it is measured by a sensor. While traditional film uses a chemical process to measure light, most modern cameras use solid-state sensors that are divided into pixels, each of which counts the number of photons that arrive over a period of time for some range of wavelengths. Accurately modeling the radiometry of how sensors measure light is an important part of simulating the process of image formation.

> 光线被相机捕获后，需要由传感器进行测量。传统胶片使用化学过程来测量光线，而大多数现代相机使用固态传感器，传感器被分成像素，每个像素在一段时间内对某个波长范围内到达的光子数量进行计数。准确建模传感器如何测量光线的辐射度量是模拟成像过程的重要组成部分。

To that end, all of pbrt's camera models use an instance of the Film class, which defines the basic interface for the classes that represent images captured by cameras. We describe two film implementations in this chapter, both of which use the PixelSensor class to model the spectral response of a particular image sensor, be it film or digital. The film and sensor classes are described in the final section of this chapter.

> 为此，pbrt 的所有相机模型都使用一个 Film 类的实例，该类定义了表示相机捕获图像的类的基本接口。本章描述两种胶片实现，它们都使用 PixelSensor 类来建模特定图像传感器（无论是胶片还是数字传感器）的光谱响应。胶片和传感器类在本章最后一节中描述。

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[相机与胶片]]

**同章节**:
- [[5.1 Camera Interface]]
- [[5.2 Projective Camera Models]]
- [[5.3 Spherical Camera]]
- [[5.4 Film and Imaging]]
