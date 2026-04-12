---
title: "RAG 与 Wiki 范式"
description: "两种 LLM 知识管理范式的根本对比：每次重新发现 vs. 持久化积累"
created: 2026-04-12
updated: 2026-04-12
tags:
  - "knowledge-management"
  - "llm"
  - "methodology"
sources:
  - "[[llm-wiki]]"
---

# RAG 与 Wiki 范式

## 核心对比

| 维度 | RAG 范式 | Wiki 范式（[[llm-wiki]]） |
|------|---------|------------------------|
| **知识存储** | 原始文档分块 + 向量索引 | LLM 生成的结构化 Wiki 页面 |
| **查询时行为** | 检索相关片段 → 从头拼凑回答 | 读取已编译的 Wiki 页面 → 综合回答 |
| **知识积累** | ❌ 无积累，每次从零开始 | ✅ 持久化复利，越用越丰富 |
| **交叉引用** | 查询时动态发现 | 写入时就已建立 |
| **矛盾检测** | 不主动检测 | 摄入时就已标记 |
| **维护成本** | 低（只需索引） | 低（LLM 自动维护） |
| **多文档综合** | 困难（需找到并拼凑多个片段） | 容易（综合已完成） |

## RAG 的局限

来自 [[Andrej Karpathy]] 的观察：

> Most people's experience with LLMs and documents looks like RAG: you upload a collection of files, the LLM retrieves relevant chunks at query time, and generates an answer. This works, but **the LLM is rediscovering knowledge from scratch on every question**. There's no accumulation.

典型 RAG 系统：NotebookLM、ChatGPT 文件上传、大多数企业 RAG 方案。

## Wiki 范式的优势

1. **知识被编译一次，然后持续更新**（compiled once, kept current）
2. **交叉引用已就位** —— 不需要在查询时动态发现关联
3. **矛盾已被标记** —— 新来源摄入时就与已有知识对比
4. **探索也产生复利** —— 好的 query 回答可以存回 Wiki 作为新页面

## 与传统 Wiki 的区别

传统人类维护的 Wiki（如企业内部 Wiki）面临的核心问题：**维护负担增长快于价值增长**，人类最终放弃维护。

LLM Wiki 解决了这个问题：LLM 不会厌烦、不会忘记更新交叉引用，一次操作可触及 15 个文件。维护成本趋近于零。

## 关联

- [[llm-wiki]] — 方法论原文
- [[知识复利]] — Wiki 持续增值的机制
- [[人机协作知识管理]] — 分工模式
