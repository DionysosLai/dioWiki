# DioWiki 页面模板

新建或大改页面时优先对齐下列 frontmatter 与结构。`created` / `updated` 用 `YYYY-MM-DD`。

## 通用 frontmatter（Concepts / Entities / Analyses）

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
  - "[[来源短名]]"
---
```

## Sources（来源摘要）

文件放在 `Wiki/Sources/`。`short` 用于索引与 `[[链接]]`（通常等于文件名）。

```yaml
---
type: source
title: "来源全称"
short: 短名
author: "[[作者实体]]"
raw: Raw/相对路径.md
source_url: https://...
tags:
  - topic
created: YYYY-MM-DD
updated: YYYY-MM-DD
data_completeness: "完整 / 部分摘要 / 标题推断等说明"
---
```

正文建议结构：

```markdown
# 来源标题

## 基本信息
| 属性 | 值 |
|------|-----|
| **全称** | ... |
| **作者** | [[...]] |
| **性质** | ... |

## 核心论点
1. ...

## 关键实体与概念
- [[实体]] / [[概念]]
```

## Entities（实体）

```markdown
# 实体名

## 基本信息
| 属性 | 值 |
|------|-----|
| **身份** | ... |
| **与本知识库的关系** | ... |

## 与本知识库的关联
...

## 核心观点（来自 [[来源]]）
- ...
```

## Concepts（概念）

```markdown
# 概念名

## 定义
...

## 要点
- ...

## 关联
- 来源：[[...]]
- 相关概念：[[...]]
```

## Analyses（分析）

```markdown
# 分析标题

## 问题 / 范围
...

## 结论
...

## 依据
- [[来源或概念]]
```

## index.md 更新

在对应分区表格中新增一行；Sources 表含「页面 / 来源 / 数据完整度」；Entities 含「页面 / 类型 / 关联来源」；Concepts 按主题分组。

## log.md 示例

```markdown
## [2026-07-30] ingest | 摄入某某文章

- 阅读 `Raw/...`，创建来源摘要 [[短名]]
- 新建/更新：[[实体A]]、[[概念B]]
- 更新 index.md、overview.md
```
