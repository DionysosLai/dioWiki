---
title: "Exercises"
source: "https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration/Exercises"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---

1. ② What kinds of scenes are worst-case scenarios for the two acceleration structures in pbrt? (Consider specific geometric configurations that the approaches will respectively be unable to handle well.) Construct scenes with these characteristics, and measure the performance of pbrt as you add more primitives. How does the worst case for one behave when rendered with the other?

> ② 什么样的场景是 pbrt 中两种加速结构的最坏情况？（考虑各自方法无法很好处理的特定几何配置。）构造具有这些特征的场景，并测量添加更多图元时 pbrt 的性能。一种方法的最坏情况在用另一种方法渲染时表现如何？

2. ② Implement a hierarchical grid accelerator where cells that have an excessive number of primitives overlapping them are refined to instead hold a finer subgrid to store its geometry. Compare both accelerator construction performance and rendering performance to a non-hierarchical grid as well as to pbrt's built-in accelerators.

> ② 实现一个分层网格加速器，当单元格中重叠的图元数量过多时，将其细化为更精细的子网格来存储几何体。将加速器构建性能和渲染性能与非分层网格以及 pbrt 的内置加速器进行比较。

3. ② *Smarter overlap tests for building aggregates*: using objects' bounding boxes to determine which sides of a kd-tree split they overlap can hurt performance by causing unnecessary intersection tests. Therefore, add a `bool Overlaps(const Bounds3f &)` method to the Shape interface that takes a rendering space bounding box and determines if the shape truly overlaps the given bound. Implement this method for Spheres and Triangles, and modify KdTreeAggregate to call it. Measure the change in pbrt's overall performance.

> ② **更智能的重叠测试**：使用物体包围盒来确定它们与 kd-tree 分割的哪一侧重叠可能因导致不必要的相交测试而损害性能。因此，向 Shape 接口添加 `bool Overlaps(const Bounds3f &)` 方法来确定形状是否真正与给定包围盒重叠。为 Sphere 和 Triangle 实现此方法，并修改 KdTreeAggregate 来调用它。测量 pbrt 整体性能的变化。

4. ② Implement "split clipping" in pbrt's BVH implementation. Read one or more papers on this topic and implement one of their approaches to subdivide primitives with large bounding boxes relative to their surface area into multiple subprimitives for tree construction. Measure the improvement for rendering actual scenes.

> ② 在 pbrt 的 BVH 实现中实现"分割裁剪"（split clipping）。阅读相关论文并实现将包围盒相对于表面积较大的图元细分为多个子图元以用于树构建的方法。测量渲染实际场景时的改进。

5. ② The 30-bit Morton codes used for the HLBVH construction algorithm in the BVHAggregate may be insufficient for scenes with large spatial extents because they can only represent steps in each dimension. Modify the BVHAggregate to use 64-bit integers with 63-bit Morton codes for HLBVHs. Compare the performance of your approach to the original one with a variety of scenes.

> ② BVHAggregate 中 HLBVH 构建算法使用的 30 位 Morton 码对于空间范围较大的场景可能不够，因为每个维度只能表示有限的步长。修改 BVHAggregate 使用 64 位整数和 63 位 Morton 码。将你的方法与原始方法在各种场景上的性能进行比较。

6. ② Investigate alternative SAH cost functions for building BVHs or kd-trees. How much can a poor cost function hurt its performance? How much improvement can be had compared to the current one?

> ② 研究用于构建 BVH 或 kd-tree 的替代 SAH 代价函数。糟糕的代价函数会对性能造成多大损害？与当前的代价函数相比能获得多少改进？

7. ③ The idea of using spatial data structures for ray intersection acceleration can be generalized to include spatial data structures that themselves hold other spatial data structures rather than just primitives. Modify pbrt to build hybrid acceleration structures—for example, using a BVH to coarsely partition the scene geometry and then uniform grids at the leaves of the tree to manage dense, spatially local collections of geometry.

> ③ 使用空间数据结构进行光线相交加速的思想可以推广到空间数据结构本身包含其他空间数据结构而不仅仅是图元的情况。修改 pbrt 来构建混合加速结构——例如，使用 BVH 粗略划分场景几何体，然后在树的叶节点使用均匀网格来管理密集的局部几何体集合。

8. ② Eisemann et al. (2007) described an even more efficient ray–box intersection test than is used in the BVHAggregate. It does more computation at the start for each ray but makes up for this work with fewer computations to do tests for individual bounding boxes. Implement their method in pbrt, and measure the change in rendering time for a variety of scenes.

> ② Eisemann 等人（2007）描述了一种比 BVHAggregate 中使用的更高效的光线-盒体相交测试。它为每条光线在开始时做更多计算，但通过减少单个包围盒测试的计算量来弥补。在 pbrt 中实现他们的方法，并测量各种场景的渲染时间变化。

9. ② Although the intersection algorithm implemented in IntersectTriangle() is watertight, a source of inaccuracy in ray–triangle intersections computed in pbrt remains: because the triangle intersection algorithm shears the vertices of the triangle, it may no longer lie in its original bounding box. Modify pbrt to fix this issue. What is the performance impact of your fix?

> ② 尽管 IntersectTriangle() 中实现的相交算法是水密的，但 pbrt 中计算的光线-三角形相交仍存在不精确的来源：由于三角形相交算法对三角形顶点进行了剪切变换，三角形可能不再位于其原始包围盒内。修改 pbrt 来修复此问题。你的修复对性能有什么影响？

10. ② Read the paper by Segovia and Ernst (2010) on memory-efficient BVHs, and implement their approach in pbrt. How does memory usage with their approach compare to that for the BVHAggregate? Compare rendering performance.

> ② 阅读 Segovia 和 Ernst（2010）关于内存高效 BVH 的论文，并在 pbrt 中实现他们的方法。他们方法的内存使用量与 BVHAggregate 相比如何？比较渲染性能。

11. ② Modify pbrt to use the "mailboxing" optimization in the KdTreeAggregate to avoid repeated intersections with primitives that overlap multiple kd-tree nodes. Measure the performance change compared to the current implementation for a variety of scenes.

> ② 修改 pbrt，在 KdTreeAggregate 中使用"邮箱"（mailboxing）优化来避免与跨多个 kd-tree 节点的图元重复相交。测量与当前实现相比在各种场景上的性能变化。

12. ② Consider a scene with an animated camera that is tracking a moving object such that there is no relative motion between the two. Modify pbrt to automatically perform this optimization when this situation occurs.

> ② 考虑一个动画摄像机跟踪运动物体、两者之间没有相对运动的场景。修改 pbrt 在出现这种情况时自动执行此优化。

13. ③ Lacewell et al. (2008) suggested augmenting the acceleration structure with a prefiltered directionally varying representation of occlusion for regions of space. Implement such an approach in pbrt, and compare its performance to the current implementation.

> ③ Lacewell 等人（2008）建议用预过滤的方向可变遮挡表示来增强空间区域的加速结构。在 pbrt 中实现这种方法，并与当前实现比较性能。
