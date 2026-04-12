---
type: concept
title: BSDF
aliases:
  - 双向散射分布函数
  - Bidirectional Scattering Distribution Function
  - BRDF
  - BTDF
source:
  - "[[pbrt]]"
related:
  - "[[光线追踪]]"
  - "[[路径追踪]]"
  - "[[辐射度量学]]"
tags:
  - rendering
  - reflection-model
  - surface-scattering
created: 2026-04-11
updated: 2026-04-11
---

# BSDF (双向散射分布函数)

## 定义

**BSDF** (Bidirectional Scattering Distribution Function) 是描述光在物体表面如何散射的函数。给定一个入射方向 ωi 和一个出射方向 ωo，BSDF f(p, ωo, ωi) 告诉我们从 ωi 入射的光有多少比例被散射到 ωo 方向。

## 层次结构

BSDF 是一个通用概念，包含几个特化的子类型：

| 函数 | 全称 | 描述 |
|------|------|------|
| **BRDF** | Bidirectional Reflectance Distribution Function | 仅描述**反射**（入射光和出射光在同一侧） |
| **BTDF** | Bidirectional Transmittance Distribution Function | 仅描述**透射**（光穿过表面） |
| **BSDF** | Bidirectional Scattering Distribution Function | BRDF + BTDF 的统一，描述光在表面两侧的所有散射 |
| **BSSRDF** | Bidirectional Scattering Surface Reflectance Distribution Function | 描述光从表面一点**进入**、从**另一点**射出的次表面散射（如牛奶、皮肤、大理石） |

## 在光传输方程中的角色

光传输方程的被积函数是：**入射辐射亮度 × BSDF × cosθ**

BSDF 决定了物体的外观——它是光传输方程中唯一依赖于材质属性的部分。

## pbrt 中的 BSDF 实现（Chapter 9）

pbrt 在 Chapter 9 中实现了多种反射模型：

- **漫反射** (Lambertian) — 向所有方向均匀反射
- **镜面反射/折射** — 完美平滑表面
- **导体反射** — 金属表面（Fresnel 导体方程）
- **介质反射** — 玻璃等透明物体
- **微表面模型** — 粗糙表面（Cook-Torrance, GGX 等）
- **头发散射** — 专用的纤维散射模型
- **测量 BSDF** — 基于实际测量数据

## 核心性质

1. **互易性 (Reciprocity)**：f(p, ωo, ωi) = f(p, ωi, ωo)
2. **能量守恒**：BSDF 对所有出射方向的积分 ≤ 1（不创造能量）
3. **非负性**：f(p, ωo, ωi) ≥ 0

## 从简单到复杂

在 Chapter 1.2.5 中，pbrt 从最基本的 BRDF 概念出发：
> "每个场景中的物体提供一个 *material*（材质），即对表面外观属性的描述。这种描述由 *bidirectional reflectance distribution function* (BRDF) 给出。"

然后逐步推广：
- BRDF → BTDF（透射）
- BRDF + BTDF → BSDF（统一散射）
- BSDF → BSSRDF（次表面散射，如 Figure 1.10 中的人头模型）

## 关联

- [[路径追踪]] — 需要按 BSDF 采样方向来计算光传输
- [[蒙特卡洛积分]] — 重要性采样时，好的采样策略应匹配 BSDF 分布
- [[辐射度量学]] — BSDF 的物理单位是 1/sr（每球面度的反射率）
- [[反射模型]] — Chapter 9 实现的各类具体 BSDF
- [[纹理与材质]] — 材质层参数化 BSDF 的具体参数值
- [[光传输方程]] — BSDF 是光传输方程被积函数的核心项
- [[体积散射]] — 相位函数是 BSDF 的体积类比
