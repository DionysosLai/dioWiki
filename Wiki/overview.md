---
title: "Wiki Overview"
description: "知识库整体概览"
created: 2026-04-10
updated: 2026-07-30
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
├── Raw/          ← 原始源材料（不可变，LLM 只读；视频转录整理除外）
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
└── .cursor/skills/ ← Agent Skills（diowiki、video-transcript 等）
```

## 三种核心操作

1. **Ingest（摄入）**：添加新源材料，LLM 阅读并整合到 Wiki 中
2. **Query（查询）**：向 Wiki 提问，LLM 搜索相关页面并综合回答
3. **Lint（检查）**：定期健康检查 — 发现矛盾、孤立页面、缺失链接等

## 当前状态

- 📊 来源摘要：7（[[数学觉醒]]、[[pbrt]]、[[llm-wiki]]、[[fcg]]、[[AI对话管理黑板理论]]、[[三角洲战术设计与双端体验]]、[[mcp-vs-cli]]）
- 📄 Wiki 页面约 57+
  - 3 个元页面（index、log、overview）
  - 7 个来源摘要
  - 9 个实体（含 [[马克的技术工作坊]]）
  - 概念含数学觉醒系、pbrt 系、LLM Wiki 系，以及 AI Agent 系：[[上下文管理]]、[[MCP]]、[[CLI作为Agent工具接口]]、[[Agent Skill]]
  - 6 个分析页面（pbrt 阅读计划系列）
- 🕐 最近操作：2026-07-30 ingest [[mcp-vs-cli]]（MCP vs CLI 工具接口）
