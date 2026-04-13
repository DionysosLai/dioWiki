---
type: source
title: "Fundamentals of Computer Graphics, Fourth Edition"
short: fcg
author:
  - "[[Steve Marschner]]"
  - "[[Peter Shirley]]"
raw: Raw/fcg/
tags:
  - computer-graphics
  - textbook
  - rendering
  - modeling
  - animation
created: 2026-04-13
updated: 2026-04-13
data_completeness: "全书完整（Preface + Ch1-26，27个文件，PDF提取，待双语翻译）"
---

# Fundamentals of Computer Graphics (4th Edition)

## 基本信息

| 属性 | 值 |
|------|-----|
| **全称** | Fundamentals of Computer Graphics |
| **版本** | 第4版 (Fourth Edition) |
| **作者** | [[Steve Marschner]], [[Peter Shirley]] |
| **出版社** | A K Peters/CRC Press |
| **俗称** | **虎书**（封面有老虎） |
| **定位** | 计算机图形学入门经典教材 |
| **页数** | 737 |

## 与 pbrt 的关系

| 维度 | 虎书 (fcg) | [[pbrt]] |
|------|-----------|---------|
| **定位** | 入门教材（本科/研究生课程用书） | 进阶参考（研究级渲染系统） |
| **覆盖面** | 广而全（26章覆盖图形学全领域） | 深而专（16章聚焦物理渲染） |
| **代码** | 无完整系统，概念和算法为主 | 完整渲染器源码（文学编程） |
| **数学门槛** | 从基础讲起 | 假设已有基础 |
| **建议** | 先读虎书，再读 pbrt | |

## 全书结构（26章）

### 基础部分（Ch1-3）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch1 | Introduction | 12 | 图形学领域概览、历史 |
| Ch2 | Miscellaneous Math | 40 | 向量、三角函数、曲线方程——数学预备 |
| Ch3 | Raster Images | 16 | 像素、颜色空间、alpha合成、图像存储 |

### 光线追踪与几何（Ch4-7）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch4 | Ray Tracing | 20 | [[光线追踪]]入门：光线-物体求交、阴影、镜面反射 |
| Ch5 | Linear Algebra | 20 | [[仿射变换]]的数学基础：线性映射、矩阵、特征值 |
| Ch6 | Transformation Matrices | 30 | 2D/3D变换矩阵、齐次坐标、法线变换 |
| Ch7 | Viewing | 20 | 视图变换、投影矩阵 |

### 渲染管线与信号处理（Ch8-9）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch8 | The Graphics Pipeline | 24 | 光栅化、裁剪、深度缓冲、逐片元操作 |
| Ch9 | Signal Processing | 50 | [[采样与重建]]、卷积、傅里叶变换、反走样 |

### 着色与纹理（Ch10-12）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch10 | Surface Shading | 10 | 漫反射、Phong/Blinn-Phong 着色模型 |
| Ch11 | Texture Mapping | 38 | [[纹理与材质]]：UV映射、MIP映射、凹凸贴图 |
| Ch12 | Data Structures for Graphics | 42 | [[BVH加速结构]]、BSP树、空间索引 |

### 高级光线追踪与采样（Ch13-14）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch13 | More Ray Tracing | 14 | 分布式光线追踪、软阴影、景深、运动模糊 |
| Ch14 | Sampling | 22 | [[蒙特卡洛积分]]、分层采样、重要性采样 |

### 建模与动画（Ch15-16）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch15 | Curves | 46 | Bézier、B-spline、NURBS曲线与曲面 |
| Ch16 | Computer Animation | 32 | 关键帧、物理模拟、角色动画、粒子系统 |

### 图形硬件与物理基础（Ch17-19）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch17 | Using Graphics Hardware | 42 | GPU编程、着色器、OpenGL/DirectX |
| Ch18 | Light | 14 | [[辐射度量学]]：辐射通量、辐照度、辐射亮度 |
| Ch19 | Color | 22 | 色度学、CIE XYZ、色彩空间 |

### 视觉与色调（Ch20-21）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch20 | Visual Perception | 44 | 人类视觉系统、亮度感知、空间/时间视觉 |
| Ch21 | Tone Reproduction | 26 | HDR→LDR色调映射 |

### 高级主题（Ch22-26）
| 章节 | 标题 | 页数 | 核心内容 |
|------|------|------|---------|
| Ch22 | Implicit Modeling | 28 | 隐式曲面、CSG、距离场 |
| Ch23 | Global Illumination | 14 | [[光传输方程]]、辐射度方法、[[路径追踪]] |
| Ch24 | Reflection Models | 16 | [[反射模型]]：[[BSDF]]、微表面理论 |
| Ch25 | Computer Graphics in Games | 22 | 游戏引擎架构、实时渲染技巧 |
| Ch26 | Visualization | 36 | 科学可视化、信息可视化 |

## 关联概念

与 [[pbrt]] 共享的概念（虎书为入门、pbrt为进阶）：
- [[光线追踪]] — Ch4 入门 vs pbrt 全书核心
- [[蒙特卡洛积分]] — Ch14 基础介绍 vs pbrt Ch2 深入
- [[仿射变换]] — Ch5-6 数学推导 vs pbrt Ch3 工程实现
- [[采样与重建]] — Ch9 信号处理理论 vs pbrt Ch8 采样器实现
- [[BVH加速结构]] — Ch12 数据结构 vs pbrt Ch7 优化实现
- [[BSDF]] / [[反射模型]] — Ch24 理论 vs pbrt Ch9 完整实现
- [[辐射度量学]] — Ch18 物理基础 vs pbrt Ch4 光谱渲染
- [[纹理与材质]] — Ch11 映射技术 vs pbrt Ch10 材质系统
- [[路径追踪]] / [[光传输方程]] — Ch23 概述 vs pbrt Ch13-14 深入

虎书独有主题：
- 图形管线与光栅化（Ch8）
- 曲线与曲面建模（Ch15）
- 计算机动画（Ch16）
- GPU编程（Ch17）
- 视觉感知（Ch20）
- 色调映射（Ch21）
- 隐式建模（Ch22）
- 游戏图形学（Ch25）
- 可视化（Ch26）
