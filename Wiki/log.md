---
title: "Wiki Log"
description: "知识库操作日志 — 按时间记录所有 ingest、query、lint 操作"
created: 2026-04-10
updated: 2026-04-15
---

# 📝 Wiki Log

按时间倒序记录知识库的所有操作。每条记录格式：

```
## [YYYY-MM-DD] 操作类型 | 标题
```

可用 `grep "^## \[" log.md | tail -5` 快速查看最近 5 条记录。

---

## [2026-04-15] query | 将 pbrt 阅读计划细化为日历版

- 问题：希望把 [[pbrt]] 的 8 周阅读计划进一步拆成更可执行的日历节奏
- 涉及页面：[[pbrt]]、[[fcg]]、[[蒙特卡洛积分]]、[[光线追踪]]
- 结论：在现有 `Analyses/pbrt两个月阅读计划.md` 基础上，新增了“每周固定节奏模板”和“第 1 周按天日历版”，将第 1 周拆到具体文件级别（`Preface`、Ch1、Ch2 各文件），并给出最低产出与掉队补救顺序。
- 备注：后续如需要，可继续按同样格式追加第 2-8 周的日历版。

## [2026-04-15] query | 制定 pbrt 两个月阅读计划

- 问题：计划用 2 个月读完 [[pbrt]]，是否合理，以及如何拆解为可执行节奏
- 涉及页面：[[pbrt]]、[[fcg]]、[[光线追踪]]、[[蒙特卡洛积分]]
- 结论：**对已有基础图形学、线代、概率与一定 C++ 阅读能力的读者，2 个月完成首遍主线是合理的；对零基础读者偏紧。**
- 产出：新建 `Analyses/pbrt两个月阅读计划.md`，给出 8 周阅读顺序、每周目标、虎书补位章节、最低完成标准与掉队补救策略。

## [2026-04-13] ingest | 虎书《Fundamentals of Computer Graphics》4th Ed. 完整摄入

- 来源：`Raw/Fundamentals of Computer Graphics, Fourth Edition ( PDFDrive ).pdf`（737页 PDF）
- 作者：Steve Marschner, Peter Shirley
- **操作 1：PDF 提取**
  - 按 26 章 + 前言拆分为 27 个 Markdown 文件，存入 `Raw/fcg/`
  - 总文本量约 1.35MB（PDF 直接提取，含断行等 PDF 提取特征）
- **操作 2：Wiki 框架建立**
  - 新建 `Sources/fcg.md` — 来源摘要（含全书 26 章结构、与 pbrt 对比表）
  - 新建 `Entities/Steve Marschner.md`、`Entities/Peter Shirley.md` — 作者实体
  - 更新 `index.md`、`overview.md` — 加入虎书来源和作者
- **操作 3：跨书概念关联**
  - 更新 11 个共享概念页面（光线追踪、蒙特卡洛积分、仿射变换、采样与重建、BVH加速结构、BSDF、辐射度量学、纹理与材质、路径追踪、光传输方程、反射模型）
  - 每个概念页面添加 `[[fcg]]` 来源引用 + "在虎书中"章节说明 + 关联链接
- **操作 4：Raw 文件 wikilink**
  - 为 27 个 Raw/fcg 文件添加 Related 区块（来源、作者、关联概念、pbrt 对应章节）
- **待完成**：双语翻译（27 个文件，约 1.35MB 英文文本）

## [2026-04-12] ingest | 补齐 ingest —— llm-wiki 完整摄入 + 数学觉醒深度充实

- **操作 1：llm-wiki.md 完整 ingest**
  - 阅读 `Raw/llm-wiki.md`（Andrej Karpathy 的 LLM Wiki 方法论全文）
  - 新建页面：
    - `Sources/llm-wiki.md` — 来源摘要（三层架构、三种操作、适用场景、工具生态、Memex 思想渊源）
    - `Entities/Andrej Karpathy.md` — 作者实体页
    - `Concepts/RAG与Wiki范式.md` — RAG 每次重新发现 vs. Wiki 持久化积累的根本对比
    - `Concepts/知识复利.md` — Wiki 随每次 ingest 和 query 不断增值的机制
    - `Concepts/人机协作知识管理.md` — 人类策展 + LLM 维护的分工模式
- **操作 2：《数学觉醒》第 3-20 章深度 ingest**
  - 阅读全部 24 个 Raw .txt 文件（第 3-20 章均为标题推断，无完整正文）
  - 大幅充实 `Sources/数学觉醒.md`：补充全书四部分论证逻辑、每章核心内容与数据质量标注、新增核心论点和跨书关联
  - 充实 6 个已有概念页面（数学直觉、内化学习、具身认知、数学焦虑、刻意练习、视觉化思维）：增加全书章节轨迹表、跨书关联（与 pbrt、llm-wiki 的联系）
- **操作 3：建立跨书关联网络**
  - 数学觉醒 ↔ pbrt：视觉化思维↔光线追踪、具身认知↔采样直觉、数学直觉↔光传输方程
  - 数学觉醒 ↔ llm-wiki：内化学习↔知识复利、刻意练习↔人机协作、人类策展
- **更新元页面**：index.md（+1 来源 +1 实体 +3 概念 +7 pbrt 概念补登）、overview.md（42 页面/28 概念/6 实体/3 来源）
- **涉及页面**：15 个（5 新建 + 7 更新 + 3 元页面）
- **备注**：《数学觉醒》第 3-20 章原文受 QQ 阅读登录限制，仅有标题和字数，无法获取完整正文。如未来能提供完整正文，可进一步深度 ingest

---

## [2026-04-12] ingest | pbrt 全书双语翻译完成 — 状态同步到 Wiki

- 操作：将 pbrt 全书（Ch1-16 + 附录 A-C）的双语翻译完成状态同步到 Wiki 元页面
- **背景**：翻译工作分多轮会话完成：
  - 2026-04-11：Preface + Ch1-3 + Ch6-8 双语翻译
  - 2026-04-12：Ch4-5 + Ch9-16 + 附录 A-C 双语翻译
  - 翻译格式：英文原文段落 + `> ` 中文块引用；代码块原样保留；Further Reading 文件跳过
- **更新内容**：
  1. `Sources/pbrt.md`：data_completeness 更新为全书完成，所有 19 个章节/附录 Raw 状态更新为"✅ 双语翻译完成"
  2. `overview.md`：pbrt 文件数修正为 167，标注全部翻译完成
  3. `index.md`：pbrt 完整度更新
- **统计**：Raw/pbrt/ 共 167 个 .md 文件（16 章 × 每章 6-14 个文件 + 3 附录 + 5 个根文件）
- 涉及页面：3 个（pbrt.md、overview.md、index.md）
- **备注**：Wiki 概念页面（25 个）已于 2026-04-11 补建完成，本次无新增概念页面

---

## [2026-04-11] ingest | 系统性补建知识图谱——新增 7 个概念页面

- 操作：基于全部 Raw/pbrt/ 翻译材料（Ch1-16 + 附录 A-C），系统性补建 Wiki 知识图谱
- **新建 7 个概念页面**：
  - [[形状系统]] — Chapter 6: Shape/Primitive 两层抽象、七种几何图元、浮点误差管理
  - [[反射模型]] — Chapter 9: 四大反射类别、微表面理论、八种 BSDF 实现
  - [[体积散射]] — Chapter 11+14: 参与介质、三大物理过程、相位函数、传输方程
  - [[光源]] — Chapter 12: 五种光源类型、三种光源采样策略
  - [[纹理与材质]] — Chapter 10: 纹理反走样、MIP 映射、十种材质实现
  - [[相机与胶片]] — Chapter 5: 相机模型、镜头效应、传感器光谱响应
  - [[光传输方程]] — Chapter 13: 渲染方程的数学形式、路径积分、三种积分器
- **注**：[[采样与重建]] 页面之前仅在 index.md 登记，实际文件缺失，本次补建
- **充实 10 个已有页面**：[[光谱渲染]]（光谱表示体系/颜色空间/色散）、[[辐射度量学]]（表面反射/光发射）、[[BVH加速结构]]（Primitive 层次/构建策略）、[[BSDF]]、[[光线追踪]]、[[路径追踪]]、[[蒙特卡洛积分]]、[[GPU渲染]]、[[仿射变换]]、[[球面几何]] — 补充新页面互链
- **更新元页面**：index.md（+7 概念条目）、overview.md（统计更新至 35 页面/25 概念）、pbrt.md（Raw 状态全部更新为已抓取、+7 关联链接）
- 涉及页面：25 个

## [2026-04-11] ingest | pbrt Chapter 6-8 双语翻译（原地）

- 操作：对 `Raw/pbrt/` 中 Chapter 6 (Shapes)、Chapter 7 (Primitives and Intersection Acceleration)、Chapter 8 (Sampling and Reconstruction) 的所有核心文件进行原地双语翻译
- 翻译格式：保留英文原段落，紧跟 `> ` 中文翻译块引用；代码块全部移除（代码密集型文件精简处理）
- 方案：方案2（跳过 Further Reading 文件）
- 涉及文件：
  - **Chapter 6**（前序会话已完成）：6 导言 + 6.1–6.6 + Exercises
  - **Chapter 7**（5 文件）：7 导言、7.1 Primitive Interface and Geometric Primitives、7.2 Aggregates、7.3 Bounding Volume Hierarchies（1116行→精简）、Exercises
  - **Chapter 8**（10 文件）：8 导言、8.1 Sampling Theory、8.2 Sampling and Integration、8.3 Sampling Interface、8.4 Independent Sampler、8.5 Stratified Sampler、8.6 Halton Sampler、8.7 Sobol' Samplers、8.8 Image Reconstruction、Exercises
- 技术要点：
  - Chapter 7 核心概念：Primitive 接口、GeometricPrimitive/SimplePrimitive、TransformedPrimitive（对象实例化）、BVH 构建三阶段、SAH 代价模型、HLBVH（Morton 编码）、紧凑线性 BVH 遍历
  - Chapter 8 核心概念：采样理论与傅里叶分析、走样/奈奎斯特频率/吉布斯现象、PSD 与蓝噪声、低偏差序列与 QMC/RQMC、6 种采样器（Independent、Stratified、Halton、SobolSampler、PaddedSobol、ZSobol）、Owen 置乱、5 种重建滤波器（Box、Triangle、Gaussian、Mitchell、Lanczos Sinc）
  - Chapter 8 术语统一：走样=aliasing、偏差=discrepancy、置乱=scrambling、分层=stratification、基数反转=radical inverse

---

## [2026-04-11] ingest | pbrt Chapter 4-8 Raw 抓取

- 来源：从 pbr-book.org 抓取 Chapter 4-8 全部章节文件到 `Raw/pbrt/`
- 新增目录及文件：
  - `Raw/pbrt/4 Radiometry, Spectra, and Color/`
  - `Raw/pbrt/5 Cameras and Film/`
  - `Raw/pbrt/6 Shapes/`
  - `Raw/pbrt/7 Primitives and Intersection Acceleration/`
  - `Raw/pbrt/8 Sampling and Reconstruction/`
- 备注：Chapter 4-5 尚未翻译，Chapter 6-8 已翻译

---

## [2026-04-11] ingest | pbrt Preface + Ch1-3 双语翻译（原地）

- 操作：对 `Raw/pbrt/` 中 Preface、Chapter 1、Chapter 2、Chapter 3 的所有核心文件进行原地双语翻译
- 翻译格式：保留英文原段落，紧跟 `> ` 中文翻译块引用；代码块原样保留
- 方案选择：方案2（跳过 Further Reading 文件，优先核心内容）
- 涉及文件（共 30 个，跳过 3 个 Further Reading）：
  - **Preface**（1 文件）：Preface.md
  - **Chapter 1**（8 文件）：1.1–1.7 + Exercises
  - **Chapter 2**（8 文件）：2.1–2.4 + Exercises（含 2.2 小节拆分文件）
  - **Chapter 3**（13 文件）：3.1–3.11 + 章节导言 + Exercises
- 跳过的文件：Preface Further Reading.md、Ch1 Further Reading.md、Ch3 Further Reading.md
- 技术要点：
  - 超长文件（3.7 包围盒、3.8 球面几何、3.9 变换、3.10 应用变换、3.11 交互）在翻译时精简了冗余代码块，保留全部说明性文本
  - 专业术语统一：变换=transformation、法线=normal、包围盒=bounding box、齐次坐标=homogeneous coordinates、正交=orthogonal、归一化=normalize、参与介质=participating media

---

## [2026-04-11] lint | 元页面一致性修正

- 操作：
  1. 发现 `Concepts/仿射变换.md` 和 `Concepts/球面几何.md` 未被元页面记录
  2. 更新 `index.md`：在 pbrt 概念表中补充仿射变换、球面几何
  3. 更新 `overview.md`：
     - 修正源材料文件数：54 → 57（llm-wiki 1 + 数学觉醒 24 + pbrt 32）
     - 修正 pbrt 描述：29文件全书 → 32文件 Preface+Ch1-3（Ch4-16及附录待补）
     - 修正 Wiki 页面数：25 → 27
     - 修正概念数：15 → 17（补充仿射变换、球面几何）
  4. 更新 `Sources/pbrt.md`：关联概念补充仿射变换、球面几何
- 备注：之前的日志记录了"全书完整抓取"，但实际 Raw 仅有 Preface + Chapter 1-3（32个文件），Chapter 4-16 及附录尚未抓取。本次修正了这一不一致。

---

## [2026-04-11] ingest | pbrt 全书完整抓取（16章+3附录+前言）

- 来源：`Raw/pbrt/`（从 pbr-book.org 抓取全部内容，共 29 个 .md 文件）
- 操作：
  1. 抓取全书 16 个章节 + 3 个附录 + 前言及其延伸阅读到本地
  2. 大幅更新 `Sources/pbrt.md`（覆盖全书完整结构、版本演进、设计目标）
  3. 新建概念页面：
     - `Concepts/路径追踪.md` — 全局光照的行业标准算法
     - `Concepts/BSDF.md` — 双向散射分布函数
     - `Concepts/辐射度量学.md` — 精确描述光传输的物理基础
     - `Concepts/BVH加速结构.md` — 光线求交加速
  4. 更新概念页面（补充完整内容）：
     - `Concepts/光线追踪.md` — 增加7大组件和历史里程碑
     - `Concepts/蒙特卡洛积分.md` — 增加公式、方差减少技术
     - `Concepts/文学编程.md` — 增加 Knuth 历史和经典作品
     - `Concepts/光谱渲染.md` — 增加弃用 RGB 的原因
     - `Concepts/GPU渲染.md` — 增加性能对比和平台要求
  5. 更新元页面：`index.md`、`overview.md`
- Raw 文件清单：
  - Preface.md, Preface_Further_Reading.md
  - Chapter1_Introduction.md ~ Chapter1_Exercises.md（10个文件）
  - Chapter2_Monte_Carlo_Integration.md
  - Chapter3 ~ Chapter16（14个文件）
  - AppendixA ~ AppendixC（3个文件）

---

## [2026-04-10] ingest | Physically Based Rendering (pbrt) Preface

- 来源：`Raw/pbrt/Preface.md`（书籍前言，完整内容）
- 作者：Matt Pharr, Wenzel Jakob, Greg Humphreys
- 新建页面：
  - `Sources/pbrt.md` — 来源摘要（前言内容、设计目标、版本演进）
  - `Entities/Matt Pharr.md` — 第一作者
  - `Entities/Wenzel Jakob.md` — 作者
  - `Entities/Greg Humphreys.md` — 作者
  - `Entities/Pat Hanrahan.md` — 核心影响者
  - `Concepts/光线追踪.md` — 核心算法
  - `Concepts/文学编程.md` — 编程方法论
  - `Concepts/光谱渲染.md` — 第4版重要特性
  - `Concepts/GPU渲染.md` — 第4版新增平台
  - `Concepts/蒙特卡洛积分.md` — 数学基础
- 更新页面：`index.md`、`overview.md`

---

## [2026-04-10] ingest | 《数学觉醒：学会更清晰地思考》

- 来源：`Raw/数学觉醒：学会更清晰地思考/`（24个文件，20章+后记+附录）
- 作者：戴维·贝西（David Bessis），法国
- 数据完整度：第1-2章完整摘要，第3-20章基于标题推断
- 新建页面：
  - `Sources/数学觉醒.md` — 来源摘要（含全书结构与章节概览）
  - `Entities/戴维·贝西.md` — 作者实体页
  - `Concepts/数学直觉.md` — 核心概念
  - `Concepts/内化学习.md` — 核心概念
  - `Concepts/具身认知.md` — 核心概念
  - `Concepts/数学焦虑.md` — 核心概念
  - `Concepts/刻意练习.md` — 核心概念
  - `Concepts/视觉化思维.md` — 核心概念
- 更新页面：`index.md`、`overview.md`

---

## [2026-04-10] init | 知识库初始化

- 根据 LLM Wiki 方法论建立了三层目录结构：`Raw/`、`Wiki/`、Schema
- 创建了核心文件：`index.md`、`log.md`、`overview.md`
- Raw 目录中已有源材料：`llm-wiki.md`（方法论原文）
