---
title: "Introduction"
source: "https://pbr-book.org/4ed/Introduction"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 1 Introduction

Rendering is the process of producing an image from the description of a 3D scene. Obviously, this is a broad task, and there are many ways to approach it. *Physically based* techniques attempt to simulate reality; that is, they use principles of physics to model the interaction of light and matter. While a physically based approach may seem to be the most obvious way to approach rendering, it has only been widely adopted in practice over the past 15 or so years.

> 渲染是从三维场景的描述中生成图像的过程。显然，这是一项范围广泛的任务，有许多方法可以实现它。*基于物理的*技术试图模拟现实；也就是说，它们使用物理学原理来建模光与物质的相互作用。虽然基于物理的方法似乎是处理渲染最显而易见的方式，但它在过去大约 15 年左右才在实践中被广泛采用。

This book describes pbrt, a physically based rendering system based on the ray-tracing algorithm. It is capable of rendering realistic images of complex scenes such as the one shown in Figure [1.1](#fig:pbrt-kroken-view). (Other than a few exceptions in this chapter that are noted with their appearance, all the images in this book are rendered with pbrt.)

> 本书描述的是 pbrt，一个基于光线追踪算法的物理渲染系统。它能够渲染复杂场景的逼真图像，如图 [1.1](#fig:pbrt-kroken-view) 所示。（除了本章中特别标注的少数例外，本书中所有图像均由 pbrt 渲染。）

Most computer graphics books present algorithms and theory, sometimes combined with snippets of code. In contrast, this book couples the theory with a complete implementation of a fully functional rendering system. Furthermore, the full source code of the system is available under an open-source license, and the full text of this book is freely available online at [pbr-book.org/4ed](https://pbr-book.org/4ed), as of November 1, 2023. Further information, including example scenes and additional information about pbrt, can be found on the website, [pbrt.org](https://pbrt.org/).

> 大多数计算机图形学书籍呈现算法和理论，有时会结合代码片段。相比之下，本书将理论与一个功能完整的渲染系统的完整实现相结合。此外，该系统的完整源代码以开源许可证提供，本书的全文自 2023 年 11 月 1 日起可在 [pbr-book.org/4ed](https://pbr-book.org/4ed) 上免费获取。更多信息，包括示例场景和关于 pbrt 的附加信息，可以在网站 [pbrt.org](https://pbrt.org/) 上找到。

![](https://pbr-book.org/4ed/kroken-view-ch1.png)

Figure 1.1: A Scene Rendered by pbrt. The *Kroken* scene features complex geometry, materials, and light transport. Handling all of these effects well in a rendering system makes it possible to render photorealistic images like this one. This scene and many others can be downloaded from the pbrt website. *(Scene courtesy of Angelo Ferretti.)*

> 图 1.1：由 pbrt 渲染的场景。*Kroken* 场景具有复杂的几何体、材质和光传输。在渲染系统中良好地处理所有这些效果使得渲染像这样的照片级真实感图像成为可能。该场景及许多其他场景可从 pbrt 网站下载。*（场景由 Angelo Ferretti 提供。）*

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[光线追踪]]
- [[文学编程]]

**同章节**:
- [[1.1 Literate Programming]]
- [[1.2 Photorealistic Rendering and the Ray-Tracing Algorithm]]
- [[1.3 pbrt System Overview]]
- [[1.4 How to Proceed through This Book]]
- [[1.6 A Brief History of Physically Based Rendering]]
- [[1.6 Using and Understanding the Code]]
