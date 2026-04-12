---
title: "Primitives and Intersection Acceleration"
source: "https://pbr-book.org/4ed/Primitives_and_Intersection_Acceleration"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## 7 Primitives and Intersection Acceleration

The classes described in the last chapter focus exclusively on representing geometric properties of 3D objects. Although the Shape interface provides a convenient abstraction for geometric operations such as intersection and bounding, it is not sufficiently expressive to fully describe an object in a scene. For example, it is necessary to bind material properties to each shape in order to specify its appearance.

> 上一章描述的类专注于表示三维物体的几何属性。尽管 Shape 接口为相交和包围等几何操作提供了便利的抽象，但它不足以完整描述场景中的物体。例如，需要为每个形状绑定材质属性以指定其外观。

pbrt's CPU and GPU rendering paths diverge in how they address this issue. The classes in this chapter implement the approach used on the CPU. On the GPU, some of the details of how properties such as materials are associated with shapes are handled by the GPU's ray-tracing APIs and so a different representation is used there; the equivalents for the GPU are discussed in Section 15.3.6.

> pbrt 的 CPU 和 GPU 渲染路径在处理这个问题上有所不同。本章的类实现了 CPU 上使用的方法。在 GPU 上，材质等属性与形状关联的部分细节由 GPU 的光线追踪 API 处理，因此使用了不同的表示方式；GPU 的等价实现在 15.3.6 节中讨论。

For the CPU, this chapter introduces the Primitive interface and provides a number of implementations that allow various properties of primitives to be specified. It then presents two additional Primitive implementations that act as aggregates—containers that can hold many primitives. These allow us to implement *acceleration structures*—data structures that help reduce the otherwise complexity of testing a ray for intersection with all objects in a scene.

> 在 CPU 端，本章引入 Primitive 接口并提供多种实现，允许指定图元的各种属性。然后介绍两种额外的 Primitive 实现——聚合体（aggregate），即可容纳许多图元的容器。它们使我们能够实现**加速结构**——帮助降低光线与场景中所有物体进行相交测试的复杂度的数据结构。

The acceleration structure, BVHAggregate, is based on building a hierarchy of bounding boxes around objects in the scene. The online edition of this book also includes the implementation of a second acceleration structure, KdTreeAggregate, which is based on adaptive recursive spatial subdivision. While many other acceleration structures have been proposed, almost all ray tracers today use one of these two. Because construction and use of intersection acceleration structures is an integral part of GPU ray-tracing APIs, the acceleration structures in this chapter are only used on the CPU.

> 加速结构 BVHAggregate 基于在场景物体周围构建包围盒层次结构。本书在线版还包含第二种加速结构 KdTreeAggregate 的实现，它基于自适应递归空间细分。尽管已有许多其他加速结构被提出，但当今几乎所有光线追踪器都使用这两种之一。由于相交加速结构的构建和使用是 GPU 光线追踪 API 的内在部分，本章的加速结构仅用于 CPU。

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**相关概念**:
- [[BVH加速结构]]
- [[形状系统]]

**同章节**:
- [[7.1 Primitive Interface and Geometric Primitives]]
- [[7.2 Aggregates]]
- [[7.3 Bounding Volume Hierarchies]]
