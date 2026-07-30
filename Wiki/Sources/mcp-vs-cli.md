---
type: source
title: "为什么越来越多的人抛弃 MCP，转向 CLI？"
short: mcp-vs-cli
author: "[[马克的技术工作坊]]"
raw: Raw/AI Agent/为什么越来越多的人抛弃 MCP，转向 CLI？.md
source_url: https://www.youtube.com/watch?v=NApOvFHCb8s
tags:
  - ai-agent
  - MCP
  - CLI
  - tools
  - token
created: 2026-07-30
updated: 2026-07-30
data_completeness: "完整（视频口播整理为文章后全文摄入）"
---

# 为什么越来越多的人抛弃 MCP，转向 CLI？

## 基本信息

| 属性 | 值 |
|------|-----|
| **全称** | 为什么越来越多的人抛弃 MCP，转向 CLI？ |
| **作者** | [[马克的技术工作坊]] |
| **性质** | YouTube 技术解说视频 → 整理文稿 |
| **发布** | 2026-04-10 |
| **主题** | Agent 工具接口：[[MCP]] vs [[CLI作为Agent工具接口]] |

## 核心论点

1. **行业转向可见**：Perplexity（放弃 MCP、转向 API/CLI）、YC CEO、OpenClaw 等实践中 CLI / 内部工具占比上升，MCP 身影变少。
2. **CLI 更省 Token**：MCP 要把全部工具的名称、描述、入参 schema 塞进上下文；GitHub MCP 一例约 44 工具、~14268 tokens（约 0.3 元/次仅工具说明）。CLI 路径通常只暴露一个 `bash` 类工具，说明十几行即可。
3. **常见 CLI 靠预训练、冷门靠文档**：`gh` / `git` / `grep` 等模型已内化；冷门或自研 CLI 用 [[Agent Skill]]（说明文档）补齐。
4. **CLI 执行链路更短**：可管道组合（`|`、`&&`），一次调用完成多步；MCP 常变成「每步都回模型调度」的多轮往返。组合式优于把多步焊死成单一 MCP 工具（需求一变就要重写）。
5. **MCP 仍有不可替代处**：JSON 参数边界更清晰（可控性）；能力面受限，适合高安全 / 共享云端环境（安全性）。
6. **格局判断**：**CLI 走向个人与轻量场景；MCP 退守企业与云端**——比例下降但不会消失。

## 关键证据（原文数据）

| 项目 | 规模 |
|------|------|
| GitHub MCP 工具数 | 44 |
| 全部工具元信息 | ~1683 行 / ~63703 字符 |
| Token | ~14268 |
| 对比 | 单个 `bash` 工具说明约十几行 |

## 关键实体与概念

- 实体：[[马克的技术工作坊]]
- 概念：[[MCP]]、[[CLI作为Agent工具接口]]、[[Agent Skill]]、[[上下文管理]]
- 提及产品/案例：Perplexity、Y Combinator、OpenClaw、Claude Code、Codex、Make、GitHub MCP
