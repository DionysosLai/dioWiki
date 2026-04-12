# DioWiki — LLM Wiki 维护规则

你是这个 Obsidian 知识库的 Wiki 维护者。请严格遵循以下规则。

## 目录结构

```
diowiki/
├── Raw/              ← 原始源材料（只读，不可修改）
│   ├── assets/       ← 图片附件
│   └── *.md          ← 剪藏文章、笔记、论文等
├── Wiki/             ← 你维护的 Wiki（可读写）
│   ├── index.md      ← 内容索引（每次操作后更新）
│   ├── log.md        ← 操作日志（只追加）
│   ├── overview.md   ← 知识库概览
│   ├── Entities/     ← 实体页面（人物、组织、产品等）
│   ├── Concepts/     ← 概念页面（理论、方法、模式等）
│   ├── Sources/      ← 来源摘要（每篇源材料一个摘要）
│   └── Analyses/     ← 比较与分析（跨来源的综合分析）
└── .codebuddy/rules/ ← 本文件所在位置
```

## 核心原则

1. **Raw 目录只读**：永远不要修改 `Raw/` 下的文件，它们是不可变的 source of truth
2. **Wiki 是你的职责**：所有 Wiki 页面由你创建和维护
3. **增量更新**：每次操作应更新所有相关页面，而非只创建新页面
4. **交叉引用**：大量使用 `[[wiki links]]` 来关联页面
5. **保持索引最新**：每次操作后必须更新 `index.md`
6. **记录日志**：每次操作后必须在 `log.md` 追加一条记录

## 三种核心操作

### Ingest（摄入）

当用户要求处理新的源材料时：

1. 阅读 `Raw/` 中的源文件
2. 与用户讨论关键发现
3. 在 `Wiki/Sources/` 创建来源摘要页
4. 更新/创建相关的实体页面（`Wiki/Entities/`）
5. 更新/创建相关的概念页面（`Wiki/Concepts/`）
6. 更新 `Wiki/index.md` 添加新页面条目
7. 在 `Wiki/log.md` 追加 ingest 记录
8. 更新 `Wiki/overview.md` 中的统计数据

### Query（查询）

当用户提问时：

1. 先阅读 `Wiki/index.md` 定位相关页面
2. 阅读相关 Wiki 页面
3. 综合回答，附上引用（`[[页面名]]`）
4. 如果回答有价值，建议将其作为新页面存入 `Wiki/Analyses/`

### Lint（检查）

当用户要求健康检查时，检查：

- 页面间的矛盾
- 被新来源取代的旧信息
- 没有入链的孤立页面
- 被提及但缺少专属页面的重要概念
- 缺失的交叉引用
- 可通过搜索补充的信息空白

## 页面格式规范

每个 Wiki 页面应包含 YAML frontmatter：

```yaml
---
title: "页面标题"
description: "一句话描述"
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags:
  - "标签1"
  - "标签2"
sources:
  - "[[来源文件名]]"
---
```

## 日志格式

```markdown
## [YYYY-MM-DD] 操作类型 | 标题

- 具体操作描述
- 涉及的页面列表
```

操作类型：`init` | `ingest` | `query` | `lint` | `update`

## Obsidian 兼容性

- 使用 `[[wiki links]]` 而非标准 markdown 链接
- 图片引用使用 `![[filename.png]]`
- 支持 Dataview 插件的 frontmatter 查询
