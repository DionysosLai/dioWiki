---
title: "Exercises"
source: "https://pbr-book.org/4ed/Geometry_and_Transformations/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
1. ① Find a more efficient way to transform axis-aligned bounding boxes by taking advantage of the symmetries of the problem: because the eight corner points are linear combinations of three axis-aligned basis vectors and a single corner point, their transformed bounding box can be found more efficiently than by the method we have presented ([Arvo 1990](https://pbr-book.org/4ed/Geometry_and_Transformations/Further_Reading.html#cite:Arvo90)).

> ① 通过利用问题的对称性，找到一种更高效的方式来变换轴对齐包围盒：由于八个角点是三个轴对齐基向量和一个角点的线性组合，它们变换后的包围盒可以比我们所介绍的方法更高效地求得（Arvo 1990）。

2. ② Instead of boxes, tighter bounds around objects could be computed by using the intersections of many nonorthogonal slabs. Extend the bounding box representation in pbrt to allow the user to specify a bound comprised of arbitrary slabs.

> ② 可以通过使用多个非正交切片的交集来代替包围盒，计算更紧密的物体边界。扩展 pbrt 中的包围盒表示，允许用户指定由任意切片组成的边界。

3. ② The [DirectionCone::BoundSubtendedDirections()](https://pbr-book.org/4ed/Geometry_and_Transformations/Spherical_Geometry.html#DirectionCone::BoundSubtendedDirections) method bounds the directions that a [Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) subtends from a given reference point by first finding a sphere that bounds the [Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) and then bounding the directions it subtends. While this gives a valid bound, it is not necessarily the smallest one possible. Derive an improved algorithm that acts directly on the bounding box, update the implementation of BoundSubtendedDirections(), and render scenes where that method is used (e.g., those that use a [BVHLightSampler](https://pbr-book.org/4ed/Light_Sources/Light_Sampling.html#BVHLightSampler) to sample light sources). How are running time and image quality affected? Can you find a scene where this change gives a significant benefit?

> ② DirectionCone::BoundSubtendedDirections() 方法通过先找到一个包围 Bounds3f 的球体，然后界定它所张开的方向，来约束从给定参考点看 Bounds3f 所张开的方向。虽然这给出了一个有效的界，但不一定是最小的。推导一种直接作用于包围盒的改进算法，更新 BoundSubtendedDirections() 的实现，并渲染使用该方法的场景（例如使用 BVHLightSampler 采样光源的场景）。运行时间和图像质量受到怎样的影响？你能找到一个该更改带来显著收益的场景吗？

4. ① Change pbrt so that it transforms [Normal3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Normals.html#Normal3f) s just like [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) s, and create a scene that gives a clearly incorrect image due to this bug. (Do not forget to revert this change from your copy of the source code when you are done!)

> ① 修改 pbrt 使其像变换 Vector3f 一样变换 Normal3f，并创建一个由于此 bug 而产生明显错误图像的场景。（完成后别忘了在你的源代码副本中恢复这一更改！）
