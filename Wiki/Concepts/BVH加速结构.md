---
type: concept
title: BVH加速结构
aliases:
  - BVH
  - Bounding Volume Hierarchy
  - 包围体层次结构
source:
  - "[[pbrt]]"
related:
  - "[[光线追踪]]"
  - "[[包围盒]]"
tags:
  - rendering
  - acceleration-structure
  - data-structure
created: 2026-04-11
updated: 2026-04-11
---

# BVH 加速结构 (Bounding Volume Hierarchy)

## 定义

BVH（Bounding Volume Hierarchy，包围体层次结构）是一种**空间加速数据结构**，用于快速排除光线与场景中绝大部分几何体的求交计算。它是现代光线追踪引擎中最常用的加速结构。

## 为什么需要加速结构

在 Chapter 1.2.2 中，pbrt 解释了加速结构的必要性：

> "蛮力方法是对场景中每个物体逐一测试射线是否相交……这种方法虽然正确，但即使对于中等复杂度的场景也非常慢。更好的方法是使用一种加速结构，能够快速排除整组物体。"

有了加速结构，光线追踪的复杂度从 O(m × n)（m 像素 × n 物体）降至近 **O(m log n)**。

## 工作原理

1. **构建阶段**：将场景中所有几何体递归地用**轴对齐包围盒 (AABB)** 包围，形成一棵树
2. **查询阶段**：测试射线是否与根节点的包围盒相交
   - 不相交 → 跳过整棵子树（快速排除）
   - 相交 → 递归测试子节点
3. **叶节点**：包含实际的几何体，执行精确的射线-物体求交

## 轴对齐包围盒 (AABB)

BVH 的基础构件是**轴对齐包围盒** (Axis-Aligned Bounding Box)。在 Chapter 3.7 中，pbrt 详细实现了 `Bounds3` 类：

- 用两个对角点（`pMin`, `pMax`）表示
- 提供 `Union()`（合并）、`Intersect()`（取交）、`Overlaps()`（重叠检测）等操作
- 支持 `Inside()`（包含测试）、`DistanceSquared()`（到点的距离）
- 可计算表面积 (`SurfaceArea()`)、体积 (`Volume()`)、最长轴 (`MaxDimension()`)

AABB 的选择理由：与方向任意的包围盒 (OBB) 相比，AABB 的射线求交测试更加高效。

## Primitive 层次结构

Chapter 7 定义了 `Primitive` 接口——在 [[形状系统|Shape]] 基础上附加材质和变换：

| Primitive | 描述 |
|-----------|------|
| **SimplePrimitive** | Shape + Material |
| **GeometricPrimitive** | Shape + Material + AreaLight + MediumInterface |
| **TransformedPrimitive** | 支持动画变换的图元 |
| **AnimatedPrimitive** | 运动模糊的时变变换 |

### 聚合体 (Aggregate)

聚合体是一种特殊的 Primitive——包含其他 Primitive 的集合，对外暴露相同的接口。BVH 就是最重要的聚合体实现。

## 构建策略

BVH 的构建质量直接影响遍历效率。pbrt 实现了多种划分策略：

- **表面积启发式 (SAH)**：估计光线遍历成本，选择使期望成本最小的划分。SAH 的核心公式将子树的遍历概率与其包围盒表面积关联
- **中点划分**：简单但不如 SAH 质量好
- **等量划分**：将图元等分，适用于均匀分布的几何体

## 在 pbrt 中的位置

BVH 的完整实现在 **Chapter 7**（Primitives and Intersection Acceleration）中。Chapter 1.2.2 指出，得益于加速结构的有效性，即使是极其复杂的场景（如 *Moana Island* 场景有超过 **1.46 亿**个独立三角形，经对象实例化后的几何复杂度达到 **数百亿** 三角形）也能在合理时间内渲染。

## 关联

- [[光线追踪]] — BVH 是光线追踪器中最关键的性能组件之一
- [[形状系统]] — BVH 使用 Shape 的包围盒，Primitive 层包装 Shape
- [[仿射变换]] — 物体实例化依赖变换
- [[GPU渲染]] — GPU 有硬件级 BVH 遍历支持 (RT Core)
- [[光源]] — BVHLightSampler 使用类似 BVH 的结构选择光源
- [[pbrt]] — Chapter 7
