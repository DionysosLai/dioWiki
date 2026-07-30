---
name: diowiki
description: >-
  Maintains the dioWiki Obsidian personal knowledge base (Raw + Wiki schema).
  Use when ingesting Raw sources, querying the wiki, linting health, creating or
  updating Wiki pages (Sources/Entities/Concepts/Analyses), editing index.md or
  log.md, or when the user mentions Obsidian wiki, dioWiki, 摄入, 查询, or 健康检查.
---

# DioWiki — Obsidian Wiki 维护

你是本仓库 Obsidian 知识库的 Wiki 维护者。操作前先读 `Wiki/index.md` 定位页面。

## 目录与权限

```
diowiki/
├── Raw/              ← 只读，不可修改（source of truth）
│   └── assets/       ← 图片附件
├── Wiki/             ← 可读写，由你维护
│   ├── index.md      ← 每次操作后更新
│   ├── log.md        ← 只追加
│   ├── overview.md   ← 统计与概览
│   ├── Entities/     ← 人物、组织、产品等
│   ├── Concepts/     ← 理论、方法、模式等
│   ├── Sources/      ← 每篇源材料一份摘要
│   └── Analyses/     ← 跨来源综合分析
└── .cursor/skills/diowiki/
```

## 核心原则

1. **一般不修改 `Raw/`**（源材料只读）。例外：用户明确要求整理视频/口播转录时，改用 `video-transcript` skill，允许改写目标 Raw 文件
2. **只写 `Wiki/`**（及本仓库 `.cursor/skills/`）
3. **增量更新**：触及所有相关页面，不只建新页
4. **交叉引用**：用 `[[wiki links]]`，图片用 `![[filename.png]]`
5. 每次操作后更新 `index.md`，并在 `log.md` **追加**一条记录
6. 有统计变化时同步 `overview.md`

## 三种核心操作

### Ingest（摄入）

用户要求处理新源材料时：

1. 阅读 `Raw/` 源文件（只读）
2. 与用户讨论关键发现（先讨论再大批量写入，除非用户要求直接入库）
3. 在 `Wiki/Sources/` 创建来源摘要
4. 更新/创建相关 `Wiki/Entities/`、`Wiki/Concepts/`
5. 更新 `Wiki/index.md`、追加 `Wiki/log.md`、更新 `Wiki/overview.md`

### Query（查询）

用户提问时：

1. 先读 `Wiki/index.md` 定位
2. 阅读相关 Wiki 页后综合回答，引用 `[[页面名]]`
3. 有复用价值时，建议写入 `Wiki/Analyses/`

### Lint（检查）

用户要求健康检查时，检查：

- 页面间矛盾
- 被新来源取代的旧信息
- 无入链孤立页
- 被提及但缺专属页的重要概念
- 缺失交叉引用
- 可用搜索补全的信息空白

## 页面与日志格式

- Frontmatter / 各类型页面模板：见 [templates.md](templates.md)
- 日志条目格式：

```markdown
## [YYYY-MM-DD] 操作类型 | 标题

- 具体操作描述
- 涉及的页面列表
```

操作类型：`init` | `ingest` | `query` | `lint` | `update`

日期用当天真实日期。日志按时间倒序追加到文件顶部记录区（最新在上，紧接格式说明之后）。

## Obsidian 约定

- 链接用 `[[wiki links]]`，不用普通 markdown 链接指向 Wiki 页
- Frontmatter 保持 YAML，便于 Dataview
- 文件名即页面名；`[[链接]]` 与文件名（不含 `.md`）一致
