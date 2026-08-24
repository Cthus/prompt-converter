---
name: prompt-converter
description: Score every user message and convert tasks to prompts.
version: 0.2.1
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [prompt, converter, workflow, every-message, scoring, AskUserQuestion]
    related_skills: []
---

# Prompt Converter Skill

> **CRITICAL: 新会话首条也必须执行本 Skill。** 每轮对话的每条用户消息，无论新旧会话，都必须先执行 Step 0 打分。

把任务/动手类输入转换成结构化的、可编辑的 Prompt，等待用户二次确认后执行。问答/寒暄/澄清类不触发但仍需打分展示。

## When to Use

- **EVERY MESSAGE in EVERY CONVERSATION** — 收到任何用户消息时首先执行本 Skill 的 Step 0 打分（新会话首条也不例外）
- 收到**任务类 / 动手类**输入（改代码、加 MCP 工具、跑桌面/浏览器操作、指向明确交付物）时进入 Step 1 转换
- 用户显式说 `转prompt`、`/p`、`/prompt` 时无视分数强制转换
- Don't use for: 概念问答（A和B区别/哪个更好）、寒暄、澄清类、已是结构化 Prompt（包含 Role/Goal/Constraints）时直接回答/执行，不触发转换
- 例外：用户本轮已是结构化草稿或明确放行信号（ok/yes/直接指令）时跳过转换与确认直接执行

## 智能识别 (Score 0.0 - 1.0) - 每轮必带

对每条输入先打分并**在回复显眼处输出一行**，分三个地带：

| 分数 | 地带 | 判定 | 行为 |
|------|------|------|------|
| `0.00 - 0.11` | 安全区 | 明确不转换 | 纯问答/寒暄/澄清、仅求观点、已是结构化或 ok/yes → 直接回答 |
| `0.12 - 0.29` | 灰色地带 | 必须反问 | **用 AskUserQuestion 问**「这算任务(转结构化prompt)还是纯问答(直接答)？」不自行决定 |
| `≥0.30` | 转换区 | 明确需转换 | 含任务动词(加/改/修/删/跑/接入/实现)、指向明确交付物(具体文件/工具/测试)、有副作用(动真实桌面/浏览器) → 转 Prompt + AskUserQuestion 确认 |

**格式示例:** `score=0.12 -> 地带: 灰 (原因: 含动词"加"但无明确交付物) -> 询问用户`

**打分依据:**
- 低分(→0): 含`什么是/为什么/怎么看/介绍一下`、无交付物、只需文本
- 高分(→0.3+): 含`写/改/修/加/跑/部署/创建/实现`、含路径/代码/文件名、需工具调用、有副作用

## How to Run

### Step 0: 智能打分 (Classify) - 每轮必输出

对 `RAW` 打分 `score 0.0-1.0` 并输出一行：
- `0.00-0.11` → 直接回答，不进入 Step 1
- `0.12-0.29` → 灰色地带 → `AskUserQuestion` 反问用户「任务还是问答？」等待选择
- `≥0.30` → 进入 Step 1 转换

### Step 1: 转换 (Convert)

输入 `RAW` 后，输出以下固定模板，已填好内容。必须用 markdown 代码块包裹，方便用户一键复制修改：

````markdown
```prompt
# Role
[根据任务推断最合适的专家角色]

# Goal
[一句话清晰目标，从 RAW 提炼]

# Context
[背景信息、项目路径、已知约束，从 RAW 和历史记忆补全]

# Constraints
- [必须遵守的限制]
- [技术栈/路径/风格要求]

# Input
RAW: "用户原话"

# Output Format
[明确交付物：代码/文档/脚本 + 文件路径 + 验证方式]

# Steps
1. [拆解执行步骤]
2. [验证步骤]
```
````

**完成标准**: 输出的代码块包含以上 7 个标题，且内容已根据 RAW 具体化，不是空模板。

### Step 2: 等待确认 (Confirm)

转换后用 `AskUserQuestion` 弹二元确认：
> 确认执行？—— 是(推荐) / 否
- 选「是」→ 进入 Step 3 直接执行
- 选「否」→ 回到可编辑状态，等用户改完回发再执行

不要在确认前开始执行任务。

### Step 3: 执行 (Execute)

当用户确认「是」或发回包含 `# Role` / `# Goal` / `# Constraints` 的修改版 Prompt 时：
- 视为最终指令
- 直接按 Prompt 执行，不再转换、不再确认
- 执行完成后按 `Output Format` 交付结果并真实验证（跑代码/读文件）

## Pitfalls

- 灰色地带一律 AskUserQuestion 反问，绝不静默决定
- 不要对已经是 Prompt 的输入再做转换，会死循环
- 转换时不要丢失用户原话的关键动词和名词
- 建议改文件但用户未径直放行时，标转换区(≥0.30)且状态置「待确认」，不自动执行
- 用户显式 `/p` 时无视分数直接转换

## Verification

- 每条输入先输出 `score=0.xx -> 地带: 安全/灰/转换 (原因: ...)` 一行
- `0.00-0.11` 时直接给答案，无 Prompt 块
- `0.12-0.29` 时 AskUserQuestion 反问
- `≥0.30` 时输出 7段式 Prompt 代码块 + AskUserQuestion 确认
