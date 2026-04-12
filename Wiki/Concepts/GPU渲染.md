---
type: concept
title: GPU渲染
aliases: [GPU Rendering, GPU rendering, Wavefront Rendering]
tags:
  - rendering
  - GPU
  - performance
sources:
  - "[[pbrt]]"
created: 2026-04-10
updated: 2026-04-11
---

# GPU 渲染 (GPU Rendering)

## 定义

利用图形处理器（GPU）的大规模并行计算能力进行光线追踪渲染。pbrt 第4版通过**波前架构 (Wavefront Architecture)** 实现 GPU 渲染。

## 性能对比

| 平台 | 渲染时间 | 相对速度 |
|------|---------|---------|
| 高端 GPU (2020) | 318.6 秒 | 1× |
| 32核 CPU | 2,669 秒 | 8× 慢 |
| 8核 CPU | 11,983 秒 | 37× 慢 |

（测试场景：1500×1500，2048 spp）

## 波前架构

- 同时处理大量光线（而非逐像素处理）
- 通过队列组织渲染工作，收集相关任务批量处理
- 几乎所有代码可在 CPU 和 GPU 上同时运行

## 平台要求

1. **统一地址空间**：CPU 和 GPU 共享内存
2. **C++17 支持**：GPU 编译器需兼容
3. **硬件光线追踪**：GPU 需有 RT Core 或类似支持

## 当前限制

- 仅支持 **NVIDIA CUDA** 平台
- ~2000 行平台特定代码（`gpu/` 目录）
- 可通过 `--wavefront` 在 CPU 上运行（主要用于调试）

## 关联

- [[光线追踪]] — GPU 提供硬件加速
- [[路径追踪]] — WavefrontPathIntegrator 实现 GPU 路径追踪
- [[BVH加速结构]] — GPU 有硬件级 BVH 遍历 (RT Core)
- [[光传输方程]] — GPU 积分器求解同一方程
- [[采样与重建]] — Z-Sobol' 采样器为 GPU 优化
- [[pbrt]] — Chapter 15 完整实现
