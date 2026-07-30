---
title: "Agent Skill"
description: "给 Agent 看的说明文档/工作流约定；在 CLI 范式下常用于补齐冷门或自研命令行工具的用法"
created: 2026-07-30
updated: 2026-07-30
tags:
  - "AI"
  - "Agent"
  - "Skill"
  - "CLI"
sources:
  - "[[mcp-vs-cli]]"
---

# Agent Skill

## 定义

**Agent Skill** 本质上是一份（或一套）**给模型看的说明文档与操作约定**：描述何时使用、如何调用、参数与注意事项。在工程里常以 `SKILL.md` 等形式存在，供 Agent 按需加载。

## 与 CLI / MCP 的关系

在 [[CLI作为Agent工具接口]] 范式下：

- **常见 CLI**（`git`、`gh`、`grep` 等）：模型预训练已见过大量用法，可不依赖 Skill 直接生成命令
- **冷门或自研 CLI**：把用法写成 Skill，等价于「按需说明书」，避免像 [[MCP]] 那样把全部工具 schema 常驻上下文

因此 Skill 是「薄工具面 + 厚领域说明」路线的配套：全局只挂 `bash`，领域知识按任务拉取。

## 要点

- 降低常驻 Token：说明按需进入上下文，而非每次请求附带完整 MCP 工具表
- 可编码工作流与仓库约定（不仅是 CLI help）
- 与本库实践相关：如 `diowiki`、`video-transcript` 等项目 Skill

## 关联

- [[CLI作为Agent工具接口]]、[[MCP]]、[[上下文管理]]
- 来源：[[mcp-vs-cli]]
