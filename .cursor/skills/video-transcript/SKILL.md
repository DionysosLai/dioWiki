---
name: video-transcript
description: >-
  Cleans video/audio transcripts into readable Chinese articles in the dioWiki
  Raw vault. Use when the user asks to 整理转录, 口播稿改文章, 视频转文字整理,
  transcript to article, clean YouTube/Bilibili captions, or when a Raw file has
  timestamps like **0:00** · or a ## Transcript section.
---

# 视频转录 → 可读文章

把口播 / 字幕 / ASR 稿整理成适合阅读与后续 Wiki 摄入的 Markdown 文章。  
**允许改写 `Raw/` 中的目标源文件**（本 skill 专属例外；一般 Wiki 维护仍勿动 Raw）。

参考成品：`Raw/AI Agent/为什么越来越多的人抛弃 MCP，转向 CLI？.md`

## 何时动手

- 用户点名某篇 Raw / 粘贴转录，要求「改成文章」「去掉时间戳」等
- 文件含 `## Transcript`、`**mm:ss** ·`、大量口头语或断句碎片

未明确目标文件时，先确认路径再写。

## 工作流

1. 读完整原文（含 YAML frontmatter）
2. 识别：时间戳段落、片头片尾、口头禅、真正论点 / 数据 / 例子
3. **原地改写**同一文件（除非用户要求另存）
4. 保留并整理 frontmatter；补一句准确的 `description`
5. 写完后用几句话说明改了什么；**默认不自动 ingest**；用户说「摄入 / 入库」再走 `diowiki`

## 必须做

- 去掉时间戳（`**0:00** ·`、`[00:00]` 等）与「Transcript」壳
- 去掉口播套话：注意了、对没错、好我们再看、这期视频到这里、点赞关注、下期再见
- 合并碎句为通顺段落；用 `##` / `###` 按论证结构分节
- **保留**：核心观点、对比逻辑、具体数字与专有名词（MCP、CLI、`ffmpeg`、人名/产品名）
- 代码 / 命令 / 工具名用行内 `` `code` ``；多步流程可用有序列表或表格
- 保留片源：`source`、嵌入链接、作者、相关视频列表（可整理为列表，勿删）
- 中文书面语，克制，不写成营销稿

## 不要做

- 不发明原文没有的论点或数据
- 不把演示口播里「我翻给你看」类过程写成虚假操作步骤；改成概括（如「把 44 个工具说明合计约 N 行」）
- 不擅自改文件名或挪出 `Raw/`
- 不顺带大改无关文件；不默认跑 Wiki ingest

## Frontmatter

保留已有字段；按需清理：

```yaml
---
title: "文章标题"
source: "https://..."
author:
  - "[[频道或作者]]"
published: YYYY-MM-DD   # 视频发布日（若有）
created: YYYY-MM-DD     # 入库日
description: "一句话概括论点，不要堆 hashtag"
tags:
  - "clippings"
  - "主题词"
---
```

- `description`：写成摘要句，不要把标签和相关链接塞进去
- `tags`：保留 `clippings`，补 2–5 个主题词
- 正文开头可保留视频嵌入 `![](url)` 与「相关视频」列表，然后 `---` 分隔进入正文

## 成稿结构模板

按内容取舍，不必硬套每一节：

```markdown
# 标题

相关视频：
- ...

---

开篇：现象 / 问题（2–4 段）

## 论点一
...

## 论点二
...

## 反方或补充优势
...

## 结论 / 格局判断
...
```

对比类视频优先用「A 的优势 → B 的优势 → 谁适合什么场景」结构。

## 质量自检

- [ ] 无时间戳、无片尾关注话术
- [ ] 可当独立文章阅读，不依赖「上一秒画面」
- [ ] 关键数字与名词未丢
- [ ] frontmatter 的 description / tags 干净
- [ ] 未触发未请求的 Wiki 摄入
