---
title: "Wiki Overview"
description: "知识库整体概览"
created: 2026-04-10
updated: 2026-05-21
tags:
  - "meta"
---

# 🌐 知识库概览

本知识库采用 **LLM Wiki** 方法论构建 — 由 LLM 增量维护的个人结构化知识库。

## 核心理念

与传统 RAG 不同，LLM 不是每次查询时重新发现知识，而是**增量构建并维护一个持久化的 Wiki**。每当添加新源材料时，LLM 会阅读、提取关键信息，并整合到现有 Wiki 中 — 更新实体页面、修订主题摘要、标注矛盾之处。知识被编译一次，然后持续更新。

## 架构

```
diowiki/
├── Raw/          ← 原始源材料（不可变，LLM 只读）
│   ├── assets/   ← 图片等附件
│   └── *.md      ← 剪藏的文章、笔记等
├── Wiki/         ← LLM 生成并维护的 Wiki 页面
│   ├── index.md  ← 内容索引
│   ├── log.md    ← 操作日志
│   ├── overview.md ← 本文件
│   ├── Entities/ ← 实体页面
│   ├── Concepts/ ← 概念页面
│   ├── Sources/  ← 来源摘要
│   └── Analyses/ ← 比较与分析
└── .codebuddy/rules/ ← Schema（LLM 行为规则）
```

## 三种核心操作

1. **Ingest（摄入）**：添加新源材料，LLM 阅读并整合到 Wiki 中
2. **Query（查询）**：向 Wiki 提问，LLM 搜索相关页面并综合回答
3. **Lint（检查）**：定期健康检查 — 发现矛盾、孤立页面、缺失链接等

## 当前状态

- 📊 源材料数量：6 来源共 221 个文件
  - llm-wiki 方法论（1 文件）— **已完整 ingest**
  - 《数学觉醒》（24 文件）— 第1-2章完整摘要，第3-20章标题推断（原文受登录限制）
  - **pbrt**（167 文件 — 全书 16 章 + 3 附录 + 前言完整抓取，**全部双语翻译完成**）
  - **虎书 fcg**（27 文件 — 全书 26 章 + 前言 PDF 提取，**待双语翻译**）
  - **三角洲战术设计与双端体验**（1 文件 — KM 文章剪藏，已清洗重复片段、SVG 图标和网页噪声）
  - **AI 对话管理黑板理论**（1 文件 — 原创整理文章，已完整摄入）
- 📄 Wiki 页面数量：51
  - 3 个元页面（index、log、overview）
  - 6 个来源摘要（[[数学觉醒]]、[[pbrt]]、[[llm-wiki]]、[[fcg]]、[[三角洲战术设计与双端体验]]、[[AI对话管理黑板理论]]）
  - 8 个实体（[[戴维·贝西]]、[[Matt Pharr]]、[[Wenzel Jakob]]、[[Greg Humphreys]]、[[Pat Hanrahan]]、[[Andrej Karpathy]]、[[Steve Marschner]]、[[Peter Shirley]]）
  - 29 个概念：
    - 数学觉醒系：[[数学直觉]]、[[内化学习]]、[[具身认知]]、[[数学焦虑]]、[[刻意练习]]、[[视觉化思维]]
    - pbrt 系：[[光线追踪]]、[[蒙特卡洛积分]]、[[文学编程]]、[[光谱渲染]]、[[GPU渲染]]、[[路径追踪]]、[[BSDF]]、[[辐射度量学]]、[[BVH加速结构]]、[[仿射变换]]、[[球面几何]]、[[采样与重建]]、[[光传输方程]]、[[形状系统]]、[[反射模型]]、[[体积散射]]、[[光源]]、[[纹理与材质]]、[[相机与胶片]]
    - LLM Wiki / AI Agent 系：[[RAG与Wiki范式]]、[[知识复利]]、[[人机协作知识管理]]、[[上下文管理]]
  - 5 个分析页面：[[pbrt两个月阅读计划]]、[[pbrt Day 3 详细计划（1.3 pbrt System Overview）]]、[[pbrt Day 4 详细计划（1.4 How to Proceed through This Book + 1.5 Using and Understanding the Code）]]、[[pbrt Day 6 详细计划（2 Monte Carlo Integration + 2.1 Monte Carlo Basics）]]、[[pbrt Day 7 详细计划（2.2 Improving Efficiency + 2.3 / 2.4 选读）]]
- 🕐 最近操作：2026-05-21 新增来源 —— 《AI 的对话管理：把 Context Window 当作一块有限黑板》，并新增[[上下文管理]]概念页
