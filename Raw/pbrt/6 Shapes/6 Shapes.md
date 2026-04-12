---
title: "Shapes"
source: "https://pbr-book.org/4ed/Shapes"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 6 Shapes

In this chapter, we will present pbrt 's abstraction for geometric primitives such as spheres and triangles. Careful abstraction of geometric shapes in a ray tracer is a key component of a clean system design, and shapes are the ideal candidate for an object-oriented approach. All geometric primitives implement a common interface, and the rest of the renderer can use this interface without needing any details about the underlying shape. This makes it possible to separate the geometric and shading subsystems of pbrt.

> 在本章中，我们将介绍 pbrt 对球体和三角形等几何图元的抽象。在光线追踪器中对几何形状进行精心抽象是实现清晰系统设计的关键，而形状是面向对象方法的理想应用对象。所有几何图元都实现一个公共接口，渲染器的其余部分可以使用该接口而无需了解底层形状的任何细节。这使得 pbrt 的几何子系统和着色子系统可以相互分离。

pbrt hides details about primitives behind a two-level abstraction. The Shape interface provides access to the basic geometric properties of the primitive, such as its surface area and bounding box, and provides a ray intersection routine. Then, the Primitive interface encapsulates additional non-geometric information about the primitive, such as its material properties. The rest of the renderer then deals only with the abstract Primitive interface. This chapter will focus on the geometry-only Shape class; the Primitive interface is a key topic of Chapter 7.

> pbrt 将图元的细节隐藏在两层抽象之后。Shape 接口提供对图元基本几何属性（如表面积和包围盒）的访问，并提供光线求交例程。然后，Primitive 接口封装了关于图元的附加非几何信息，例如其材质属性。渲染器的其余部分只需处理抽象的 Primitive 接口。本章将聚焦于仅涉及几何的 Shape 类；Primitive 接口是第 7 章的核心主题。

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[形状系统]]

**同章节**:
- [[6.1 Basic Shape Interface]]
- [[6.2 Spheres]]
- [[6.3 Cylinders]]
- [[6.4 Disks]]
- [[6.5 Triangle Meshes]]
- [[6.6 Bilinear Patches]]
- [[6.7 Curves]]
- [[6.8 Managing Rounding Error]]
