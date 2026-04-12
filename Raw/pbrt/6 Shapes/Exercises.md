---
title: "Exercises"
source: "https://pbr-book.org/4ed/Shapes/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---

1. ② One nice property of mesh-based shapes like triangle meshes and subdivision surfaces is that the shape's vertices can be transformed into rendering space, so that it is not necessary to transform rays into object space before performing ray intersection tests. Interestingly enough, it is possible to do the same thing for ray–quadric intersections. The implicit forms of the quadrics in this chapter were all of the form where some of the constants were zero. More generally, we can define quadric surfaces by a symmetric matrix Q. Given this representation, first show that the matrix representing a quadric transformed by the matrix M is M⁻ᵀQM⁻¹. Next, substitute the ray equation into the more general quadric equation to compute coefficients for the quadratic equation. Now implement this approach in pbrt and use it instead of the original quadric intersection routines. How does performance compare to the original scheme?

> ② 基于网格的形状（如三角形网格和细分曲面）的一个好特性是形状的顶点可以变换到渲染空间，因此不需要在执行光线交点测试之前将光线变换到物体空间。有趣的是，对于光线-二次曲面交点也可以做同样的事情。本章中二次曲面的隐式形式都是某些常数为零的形式。更一般地，我们可以用一个对称矩阵 Q 定义二次曲面。给定此表示，首先证明经矩阵 M 变换的二次曲面的矩阵表示为 M⁻ᵀQM⁻¹。然后将光线方程代入更一般的二次方程来计算二次方程的系数。现在在 pbrt 中实现这种方法并用它替代原来的二次曲面交点例程。性能与原方案相比如何？

2. ② Transforming the object-space bounding box of a quadric to rendering space does not necessarily give an optimal bounding box. However, the matrix form of a quadric described in Exercise 6.1 can also be applied to computing optimal bounds. Read the article by Barnes (2014) on this topic and implement the approach he described in pbrt. How much are bounding boxes improved with this approach? Measure the effect of your changes on rendering performance for a scene with many transformed quadrics.

> ② 将二次曲面的物体空间包围盒变换到渲染空间不一定能给出最优包围盒。然而，练习 6.1 中描述的二次曲面的矩阵形式也可以应用于计算最优包围。阅读 Barnes (2014) 关于此主题的文章，并在 pbrt 中实现他描述的方法。使用此方法包围盒改善了多少？测量你的更改对包含许多变换二次曲面的场景渲染性能的影响。

3. ① Improve the object-space bounding box routines for the quadrics to properly account for φ_max, and compute tighter bounding boxes when possible. How much does this improve performance when rendering scenes with partial quadric shapes?

> ① 改进二次曲面的物体空间包围盒例程，以正确考虑 φ_max，并在可能时计算更紧密的包围盒。渲染包含部分二次曲面形状的场景时，这能改善多少性能？

4. ② There is room to optimize the implementations of the various quadric primitives in pbrt in a number of ways. For example, for complete spheres some of the tests in the intersection routine related to partial spheres are unnecessary. Furthermore, some of the quadrics have calls to trigonometric functions that could be turned into simpler expressions. Investigate ways to speed up these methods. How much does doing so improve the overall run time of pbrt when rendering scenes with quadrics?

> ② 有空间通过多种方式优化 pbrt 中各种二次曲面原语的实现。例如，对于完整球体，交点例程中与部分球体相关的一些测试是不必要的。此外，一些二次曲面调用了三角函数，这些可以转换为更简单的表达式。研究加速这些方法的途径。这样做在渲染包含二次曲面的场景时能将 pbrt 的整体运行时间改善多少？

5. ① Fix the buggy Sphere::Sample() and Disk::Sample() methods, which currently do not properly account for partial spheres and disks when they sample points on the surface. Create a scene that demonstrates the error from the current implementations and for which your solution is clearly an improvement.

> ① 修复有 bug 的 Sphere::Sample() 和 Disk::Sample() 方法，它们目前在采样表面上的点时没有正确处理部分球体和圆盘。创建一个场景来演示当前实现的错误，并展示你的解决方案明显是一个改进。

6. ② It is possible to derive a sampling method for cylinder area light sources that only chooses points over the visible area as seen from the receiving point, similar to the improved sphere sampling method in this chapter. Write a new implementation of Cylinder::Sample() that implements such an algorithm. Verify that pbrt still generates correct images with your method, and measure how much the improved version reduces variance for a fixed number of samples taken. How much does it improve efficiency?

> ② 可以推导出一种圆柱体面光源的采样方法，该方法仅选择从接收点看到的可见区域上的点，类似于本章中改进的球体采样方法。编写一个实现此算法的新 Cylinder::Sample() 实现。验证 pbrt 使用你的方法仍然生成正确的图像，并测量改进版本在固定采样数下减少了多少方差。它提高了多少效率？

7. ② Implement one of the approaches for sampling the spheres according to the projected solid angle in their visible region. Measure the change in pbrt's execution time when the alternative algorithm is used and discuss your results. Then, measure the MSE of pbrt's current approach as well as your approach for a few scenes with spherical light sources, using an image rendered with thousands of samples per pixel as a reference.

> ② 实现按可见区域中投影立体角采样球体的方法之一。测量使用替代算法时 pbrt 执行时间的变化并讨论你的结果。然后，使用每像素数千个样本渲染的图像作为参考，测量 pbrt 当前方法和你的方法在几个包含球形光源的场景中的 MSE。

8. ① Currently pbrt recomputes the partial derivatives ∂p/∂u and ∂p/∂v for triangles every time they are needed, even though they are constant for each triangle. Precompute these vectors and analyze the speed/storage trade-off, especially for large triangle meshes.

> ① 目前 pbrt 每次需要时都会重新计算三角形的偏导数 ∂p/∂u 和 ∂p/∂v，尽管它们对每个三角形是常量。预计算这些向量并分析速度/存储的权衡，特别是对于大型三角形网格。

9. ② Implement a general polygon primitive that supports an arbitrary number of vertices and convex or concave polygons as a new Shape in pbrt. You can assume that a valid polygon has been provided and that all the vertices lie on the same plane. An efficient technique for computing ray–polygon intersections is to find the plane equation, compute the intersection of the ray with that plane, and project to 2D for a point-in-polygon test.

> ② 实现一个支持任意数量顶点以及凸多边形或凹多边形的通用多边形原语，作为 pbrt 中的新 Shape。你可以假设提供了有效的多边形，且所有顶点都在同一平面上。计算光线-多边形交点的一种高效技术是找到平面方程，计算光线与该平面的交点，然后投影到 2D 进行点-在-多边形内测试。

10. ② Constructive solid geometry (CSG) is a solid modeling technique where complex shapes are built up by considering the union, intersection, and differences of more primitive shapes. Add support for CSG to pbrt and render images that demonstrate interesting shapes that can be rendered using CSG.

> ② 构造实体几何 (CSG) 是一种实体建模技术，通过考虑更基本形状的并集、交集和差集来构建复杂形状。在 pbrt 中添加 CSG 支持，并渲染展示可以使用 CSG 渲染的有趣形状的图像。

11. ② *Procedurally described parametric surfaces*: Write a Shape that takes a general mathematical expression of the form f(u,v) that describes a parametric surface. Evaluate the given function at a grid of positions, and create a bilinear patch mesh that approximates the given surface.

> ② *程序化描述的参数曲面*：编写一个 Shape，接受描述参数曲面的一般数学表达式 f(u,v)。在位置网格上求值给定函数，并创建一个近似给定曲面的双线性面片网格。

12. ② *Adaptive curve refinement*: Adjust the number of levels of recursive refinement used for intersection with Curve shapes based on the on-screen area that they cover. One approach is to take advantage of the RayDifferential class, which represents the image space area that a given ray represents.

> ② *自适应曲线细化*：根据 Curve 形状在屏幕上覆盖的面积调整用于交点计算的递归细化级别数。一种方法是利用 RayDifferential 类，它表示给定光线所代表的图像空间面积。

13. ② Implement one of the more efficient ray–curve intersection algorithms described by Reshetov (2017) or by Reshetov and Luebke (2018). Measure the performance of pbrt's current Curve implementation as well as your new one and discuss the results.

> ② 实现 Reshetov (2017) 或 Reshetov 和 Luebke (2018) 描述的更高效的光线-曲线交点算法之一。测量 pbrt 当前 Curve 实现和你新实现的性能并讨论结果。

14. ③ *Ray-tracing point-sampled geometry*: Implement a method for intersecting rays with collections of oriented point samples in space, as described by Schaufler and Jensen (2000). Their algorithm probabilistically determines that an intersection has occurred when a ray approaches a sufficient local density of point samples.

> ③ *光线追踪点采样几何体*：实现一种将光线与空间中定向点样本集合求交的方法，如 Schaufler 和 Jensen (2000) 所述。他们的算法在光线接近足够的点样本局部密度时概率性地确定发生了交点。

15. ③ *Deformation motion blur*: The TransformedPrimitive supports animated shapes via transformations that vary over time. However, this is not general enough to represent a triangle mesh where each vertex has a different motion. Implement a more general Triangle or BilinearPatch shape that supports specifying vertex positions at the start and end of frame and interpolates between them.

> ③ *变形运动模糊*：TransformedPrimitive 通过随时间变化的变换支持动画形状。然而，这不够通用，无法表示每个顶点有不同运动的三角形网格。实现一个更通用的 Triangle 或 BilinearPatch 形状，支持指定帧开始和结束时的顶点位置，并在它们之间进行插值。

16. ③ *Implicit functions*: Just as implicit definitions of the quadric shapes are useful for deriving ray-intersection algorithms, more complex implicit functions can also define interesting shapes. Implement a method for finding ray intersections with implicit surfaces and add it to pbrt.

> ③ *隐式函数*：正如二次曲面形状的隐式定义对推导光线交点算法很有用一样，更复杂的隐式函数也可以定义有趣的形状。实现一种求光线与隐式曲面交点的方法并将其添加到 pbrt。

17. ③ *L-systems*: A very successful technique for procedurally modeling plants was introduced to graphics by Alvy Ray Smith (1984), who applied Lindenmayer systems (L-systems) to model branching plant structures. Add an L-system primitive to pbrt that takes a grammar as input and evaluates it to create the shape it describes.

> ③ *L 系统*：一种非常成功的程序化植物建模技术由 Alvy Ray Smith (1984) 引入图形学，他应用 Lindenmayer 系统（L 系统）来建模分支植物结构。在 pbrt 中添加一个 L 系统原语，接受语法作为输入并求值以创建它描述的形状。

18. ① Given an arbitrary point p, what bound on the error from applying a scale transformation of (2,1,4) is given by Equation (6.30)? How much error is actually introduced?

> ① 给定任意点 p，由方程 (6.30) 给出的应用缩放变换 (2,1,4) 的误差界是多少？实际引入了多少误差？

19. ② The quadric shapes all use the Interval class for their intersection tests in order to bound the error in the computed t value so that intersections behind the ray origin are not incorrectly reported. First, measure the performance difference when using regular Floats for one or more quadrics. Next, manually derive conservative error bounds for t values and implement your method.

> ② 所有二次曲面形状都在其交点测试中使用 Interval 类来限制计算 t 值中的误差，以便不会错误地报告光线原点后面的交点。首先，测量对一个或多个二次曲面使用普通 Float 时的性能差异。然后手动推导 t 值的保守误差界并实现你的方法。

20. ② One detail thwarts the watertightness of the current Triangle shape implementation: the translation and shearing of triangle vertices introduces round-off error, which must be accounted for in the extent of triangles' bounding boxes. Modify pbrt to incorporate a solution to this shortcoming. Can you find scenes where small image errors are eliminated thanks to your fix?

> ② 一个细节阻碍了当前 Triangle 形状实现的水密性：三角形顶点的平移和剪切引入了舍入误差，这必须在三角形包围盒的范围中加以考虑。修改 pbrt 以纳入此缺陷的解决方案。你能找到由于你的修复而消除了小图像错误的场景吗？

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[形状系统]]

**同章节**:
- [[6 Shapes]]
- [[6.1 Basic Shape Interface]]
- [[6.2 Spheres]]
- [[6.3 Cylinders]]
- [[6.4 Disks]]
- [[6.5 Triangle Meshes]]
- [[6.6 Bilinear Patches]]
- [[6.7 Curves]]
- [[6.8 Managing Rounding Error]]
