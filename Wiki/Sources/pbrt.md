---
type: source
title: "Physically Based Rendering: From Theory to Implementation (4th Edition)"
short: pbrt
author:
  - "[[Matt Pharr]]"
  - "[[Wenzel Jakob]]"
  - "[[Greg Humphreys]]"
raw: Raw/pbrt/
source_url: https://pbr-book.org/4ed/contents
tags:
  - rendering
  - ray-tracing
  - computer-graphics
  - physically-based
created: 2026-04-10
updated: 2026-04-12
data_completeness: "全书完整（Preface + Ch1-16 + 附录A-C，167个文件全部抓取，全部双语翻译完成）"
---

# Physically Based Rendering: From Theory to Implementation

## 基本信息

| 属性 | 值 |
|------|-----|
| **全称** | Physically Based Rendering: From Theory to Implementation |
| **版本** | 第4版 (4th Edition) |
| **作者** | [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]] |
| **出版社** | MIT Press |
| **在线地址** | [pbr-book.org/4ed](https://pbr-book.org/4ed/contents) |
| **许可** | CC BY-NC-ND 4.0（全文自 2023-11-01 在线免费阅读） |
| **核心影响者** | [[Pat Hanrahan]]（斯坦福，图灵奖得主） |
| **配套系统** | pbrt 渲染器（开源） |

## 核心论点

1. **Literate Programming（文学编程）** 是呈现渲染算法的最佳方式——代码即文档，文档即代码
2. **基于物理的渲染** 通过模拟光与物质的真实交互来生成逼真图像
3. 系统设计应同时追求 **完整性**、**教学性** 和 **物理正确性**
4. **光线追踪** 是实现光真实感渲染的核心算法
5. **蒙特卡洛积分** 是求解高维渲染积分的唯一实用数值方法

## 全书结构（四大部分）

### 第一部分：系统基础（Chapters 1-4）

| 章节 | 标题 | 核心内容 | Raw 状态 |
|------|------|---------|----------|
| Ch 1 | Introduction | [[文学编程]]、[[光线追踪]]算法7大组件、系统架构、渲染简史 | ✅ 双语翻译完成（9个文件） |
| Ch 2 | [[蒙特卡洛积分]] | 概率基础、MC估计量、方差、重要性采样、MIS、俄罗斯轮盘赌、反函数采样、分布变换 | ✅ 双语翻译完成（8个文件） |
| Ch 3 | Geometry & Transformations | 坐标系、n-Tuple基类、向量/点/法线/射线/包围盒、[[球面几何]]、[[仿射变换]]、交互记录 | ✅ 双语翻译完成（14个文件） |
| Ch 4 | Radiometry, Spectra & Color | [[辐射度量学]]（通量/强度/辐照度/辐射亮度）、光谱分布、颜色空间 | ✅ 双语翻译完成（9个文件） |

### 第二部分：图像形成与场景几何（Chapters 5-8）

| 章节 | 标题 | 核心内容 | Raw 状态 |
|------|------|---------|----------|
| Ch 5 | Cameras & Film | 相机接口、透视/正交/球面相机、胶片类、传感器响应模型 | ✅ 双语翻译完成（7个文件） |
| Ch 6 | Shapes | Shape接口、球体/圆柱/圆盘/三角网格/双线性面片/曲线、浮点误差管理 | ✅ 双语翻译完成（11个文件） |
| Ch 7 | Primitives & Acceleration | Primitive接口、[[BVH加速结构]]、kd-tree | ✅ 双语翻译完成（6个文件） |
| Ch 8 | Sampling & Reconstruction | 采样理论/傅里叶分析、6种采样器实现、图像重构滤波器 | ✅ 双语翻译完成（11个文件） |

### 第三部分：光与散射（Chapters 9-12）

| 章节 | 标题 | 核心内容 | Raw 状态 |
|------|------|---------|----------|
| Ch 9 | Reflection Models | [[BSDF]]接口、漫反射/镜面/导体/介质/微表面/粗糙介质/头发散射/测量BSDF | ✅ 双语翻译完成（12个文件） |
| Ch 10 | Textures & Materials | 纹理采样与反走样、纹理坐标生成、材质接口与实现 | ✅ 双语翻译完成（8个文件） |
| Ch 11 | Volume Scattering | 体积散射过程、透过率、相位函数、均匀/非均匀介质 | ✅ 双语翻译完成（7个文件） |
| Ch 12 | Light Sources | 光源接口、点光源/方向光/面光源/环境光、多光源采样 | ✅ 双语翻译完成（9个文件） |

### 第四部分：光传输算法（Chapters 13-16）

| 章节 | 标题 | 核心内容 | Raw 状态 |
|------|------|---------|----------|
| Ch 13 | Light Transport I: Surface | 光传输方程推导、[[路径追踪]]算法、SimplePathIntegrator、PathIntegrator | ✅ 双语翻译完成（7个文件） |
| Ch 14 | Light Transport II: Volume | 传输方程、体积积分器、分层材质散射 | ✅ 双语翻译完成（6个文件） |
| Ch 15 | Wavefront Rendering on GPUs | [[GPU渲染]]波前架构、WavefrontPathIntegrator、CUDA支持、10-100×加速 | ✅ 双语翻译完成（6个文件） |
| Ch 16 | Retrospective & Future | 系统演化回顾、替代架构、新兴技术、未来展望 | ✅ 双语翻译完成（7个文件） |

### 附录

| 附录 | 标题 | 核心内容 | Raw 状态 |
|------|------|---------|----------|
| A | Sampling Algorithms | 别名方法、水库采样、拒绝采样、1D/多维函数采样 | ✅ 双语翻译完成（8个文件） |
| B | Utilities | 数学基础、内存管理、并行化、统计与性能分析 | ✅ 双语翻译完成（10个文件） |
| C | Processing Scene Description | 场景文件解析、图形状态管理、对象创建、扩展机制 | ✅ 双语翻译完成（7个文件） |

## 版本演进

| 版本 | 年份 | 重大变化 |
|------|------|---------|
| 第1版 | 2004 | 初始版本，插件架构，HDR色调映射 |
| 第2版 | 2010 | 移除插件架构，多核并行，次表面散射，Metropolis光传输 |
| 第3版 | 2016 | 双向路径追踪，改进次表面散射，数值稳健求交，随机渐进光子映射 |
| 第4版 | 2023 | **光谱渲染**（弃用RGB）、GPU渲染、波前架构、改进体积散射、多光源采样 |

## 设计三目标

1. **Complete（完整）**：不缺少高质量商业渲染系统的关键功能
2. **Illustrative（教学性）**：选择最优雅的算法，代码可读性优先
3. **Physically Based（物理正确）**：使用正确的物理单位和概念，计算物理正确的图像

## 第4版关键技术创新

- 🌈 **光谱渲染**：完全弃用RGB，使用波长采样的光谱分布
- 🎮 **GPU渲染**：CUDA波前架构，10-100×性能提升
- 💡 **多光源采样**：高效处理数百万光源的场景
- 🌫️ **改进体积散射**：支持发光体积、色散介质
- 🔬 **微表面理论**：改进的粗糙介质BSDF

## 关联概念

- [[光线追踪]] — 核心算法
- [[蒙特卡洛积分]] — 数学基础
- [[光传输方程]] — 渲染方程——所有光传输算法的理论基础
- [[文学编程]] — 编程方法论
- [[光谱渲染]] — 第4版核心特性
- [[GPU渲染]] — 第4版新增平台
- [[路径追踪]] — 光传输算法
- [[BVH加速结构]] — 求交加速
- [[BSDF]] — 表面散射描述
- [[辐射度量学]] — 物理基础
- [[仿射变换]] — 齐次坐标与 4×4 矩阵变换（Chapter 3）
- [[球面几何]] — 立体角与球坐标系（Chapter 3）
- [[采样与重建]] — 傅里叶分析、低偏差序列、6 种采样器与 5 种滤波器（Chapter 8）
- [[形状系统]] — Shape/Primitive 两层抽象、七种几何图元（Chapter 6-7）
- [[反射模型]] — 从漫反射到微表面的完整反射模型体系（Chapter 9）
- [[纹理与材质]] — 纹理参数化 BSDF、MIP 映射反走样（Chapter 10）
- [[体积散射]] — 参与介质中的光散射（Chapter 11 + 14）
- [[光源]] — 光源类型与多光源采样策略（Chapter 12）
- [[相机与胶片]] — 相机模型、胶片与传感器响应（Chapter 5）
- [[数学直觉]] — 跨书关联（与《数学觉醒》共享"直觉与形式化"主题）
