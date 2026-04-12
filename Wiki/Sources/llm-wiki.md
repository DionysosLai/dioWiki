---
type: source
title: "LLM Wiki"
short: llm-wiki
author: "[[Andrej Karpathy]]"
raw: Raw/llm-wiki.md
source_url: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
tags:
  - knowledge-management
  - llm
  - methodology
  - obsidian
created: 2026-04-12
updated: 2026-04-12
data_completeness: "完整（原文全文）"
---

# LLM Wiki

## 基本信息

| 属性 | 值 |
|------|-----|
| **全称** | LLM Wiki |
| **作者** | [[Andrej Karpathy]] |
| **性质** | 方法论 / 设计模式（idea file） |
| **发布形式** | [GitHub Gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) |
| **目标** | 用 LLM 增量构建和维护个人结构化知识库 |

## 核心论点

1. **RAG 的局限**：传统 RAG（如 NotebookLM、ChatGPT 文件上传）在每次查询时重新发现知识，没有积累；需要综合多文档的微妙问题，每次都要从头拼凑
2. **Wiki 是持久化的复利资产**：LLM 不是每次检索原始文档，而是**增量构建并维护一个持久化 Wiki** —— 交叉引用已就位、矛盾已标记、综合已完成，每添加一个来源和每提出一个问题，Wiki 都在变得更丰富
3. **人类策展，LLM 维护**：你负责选择来源、引导分析、提出好问题；LLM 负责摘要、交叉引用、归档、记账等一切繁琐工作
4. **维护成本趋近于零**：人类放弃 Wiki 是因为维护负担增长快于价值增长；LLM 不会厌烦、不会忘记更新交叉引用，一次操作可触及 15 个文件

## 三层架构

| 层级 | 说明 | 权限 |
|------|------|------|
| **Raw Sources** | 原始源材料（文章、论文、图片、数据文件） | 不可变，LLM 只读，source of truth |
| **Wiki** | LLM 生成并维护的 Markdown 文件集合 | LLM 可读写，人类只读 |
| **Schema** | 告诉 LLM Wiki 结构、约定和工作流的配置文件（如 CLAUDE.md / AGENTS.md） | 人类与 LLM 共同演进 |

## 三种核心操作

### Ingest（摄入）

添加新源材料时：LLM 阅读原文 → 与用户讨论关键发现 → 写来源摘要 → 更新索引 → 更新相关实体/概念页面 → 追加日志。一个来源可能触及 10-15 个 Wiki 页面。

- 推荐**逐一摄入**并保持参与（阅读摘要、检查更新、引导重点）
- 也可批量摄入，但监督较少

### Query（查询）

向 Wiki 提问：LLM 搜索相关页面 → 阅读 → 综合回答并附引用。

- **关键洞察**：好的回答应该被存回 Wiki 作为新页面（比较、分析、发现的联系），让探索也产生复利
- 输出形式灵活：Markdown 页面、比较表、Marp 幻灯片、matplotlib 图表、canvas 等

### Lint（检查）

定期健康检查：
- 页面间矛盾
- 被新来源取代的旧信息
- 无入链的孤立页面
- 被提及但缺少专属页面的重要概念
- 缺失的交叉引用
- 可通过搜索补充的信息空白

## 两个关键元文件

| 文件 | 性质 | 用途 |
|------|------|------|
| **index.md** | 内容导向 | Wiki 全部页面的目录，LLM 查询时先读索引定位相关页面，中等规模（~100来源、~数百页面）下无需嵌入式 RAG |
| **log.md** | 时间导向 | 只追加的操作时间线，可用 `grep` 快速查看最近记录 |

## 适用场景

- **个人成长**：目标跟踪、健康、心理、自我提升 —— 日记、文章、播客笔记的结构化积累
- **研究**：数周/数月深入一个主题 —— 论文、报告的增量综合与演进论点
- **读书**：逐章归档，构建角色、主题、情节线的伴读 Wiki（如个人版 Tolkien Gateway）
- **团队/业务**：由 LLM 维护的内部 Wiki，输入来自 Slack、会议记录、项目文档、客户电话
- **竞品分析、尽职调查、旅行规划、课程笔记、兴趣深钻** —— 任何需要持续积累和组织知识的场景

## 工具生态

| 工具 | 用途 |
|------|------|
| **Obsidian** | Wiki 的"IDE"——浏览、图谱视图、实时查看 LLM 编辑 |
| **Obsidian Web Clipper** | 浏览器扩展，将网页文章转换为 Markdown |
| **Dataview 插件** | 对 YAML frontmatter 的动态查询 |
| **Marp** | Markdown 幻灯片 |
| **qmd** | 本地 Markdown 搜索引擎（BM25 + 向量混合搜索，支持 CLI 和 MCP） |
| **Git** | Wiki 就是一个 git 仓库，天然获得版本历史、分支、协作 |

## 思想渊源

与 Vannevar Bush 1945 年提出的 **Memex** 异曲同工 —— 一个私人策展的知识存储，文档间有关联路径。Bush 的愿景比今天的 Web 更接近这个模式：私有、主动策展、文档间的连接与文档本身同样有价值。Bush 无法解决的"谁来维护"问题，由 LLM 解决了。

## 关联概念

- [[RAG与Wiki范式]] — RAG 每次重新发现 vs. Wiki 持久化积累的根本区别
- [[知识复利]] — Wiki 随每次 ingest 和 query 不断增值的机制
- [[人机协作知识管理]] — 人类策展 + LLM 维护的分工模式

## 关联实体

- [[Andrej Karpathy]] — 作者，前 Tesla AI 总监、OpenAI 联合创始人
