# prompt-converter

[![GitHub stars](https://img.shields.io/github/stars/Cthus/prompt-converter?style=social)](https://github.com/Cthus/prompt-converter)
[![Release](https://img.shields.io/github/v/release/Cthus/prompt-converter)](https://github.com/Cthus/prompt-converter/releases/tag/v0.2.0)
[![License](https://img.shields.io/github/license/Cthus/prompt-converter)](./LICENSE)
[![Hermes](https://img.shields.io/badge/Hermes-Skill-blue)](https://github.com/Cthus/prompt-converter)
[![Claude Code](https://img.shields.io/badge/Claude_Code-VSCode-purple)](https://github.com/Cthus/prompt-converter)

> ⭐ 点个 Star 就是最装逼的支持 — 把随口一句任务，转换成可编辑的 7 段式结构化 Prompt，确认后再执行。Hermes + Claude Code 双端通用。

将模糊的自然语言输入，标准化为可审查、可复用的结构化 Prompt。内置三档智能识别 + AskUserQuestion 确认，显著减少歧义返工。

---

## Overview

`prompt-converter` 解决日常协作中的核心痛点：用户一句话很短、信息不全，模型容易猜错。有了它：

1. 你发粗话（如“把检测改一下”）
2. 我先打分并转成 7 段式 Prompt 回显给你
3. 你改完发回，我按改版直接执行，不再二次猜测

同一份 `SKILL.md` 同时兼容 **Hermes Agent** 和 **Claude Code (VSCode 插件)**，零改动复用。

## When to Use

| 触发 | 示例 | 行为 |
|------|------|------|
| ✅ 任务/动手类 | `写个斗鱼弹幕爬虫存到D盘` `把这个 Skill 推到 GitHub` | 转 Prompt + 确认后执行 |
| ❌ 概念问答 | `什么是 MCP` `A 和 B 区别` | 直接回答，不触发 |
| ❌ 寒暄/澄清 | `你好` `是这个意思吗` | 直接回答 |
| 🔧 强制触发 | `/p 帮我修一下检测` | 无视分数直接转 |

> 例外：本轮已是结构化 Prompt（含 `Role/Goal/Constraints`）或明确放行信号（`ok`/`可以`）时，跳过转换直接执行。

## How it Works

### 智能识别 — 每轮必带 Score

每条输入显眼处输出一行，便于你监视分类决策：

```
score=0.42 -> 地带: 转换 (原因: 含动词"写"且指向明确交付物"爬虫")
score=0.08 -> 地带: 安全 (原因: 纯问答，无交付物)
score=0.22 -> 地带: 灰 (原因: 含动词"上传"但无明确目标平台) -> 询问用户
```

| 分数 | 地带 | 判定 | 行为 |
|------|------|------|------|
| `0.00 – 0.11` | 安全区 | 明确不转换 | 直接回答 |
| `0.12 – 0.29` | 灰色地带 | 必须反问 | `AskUserQuestion` 问「任务还是问答？」 |
| `≥ 0.30` | 转换区 | 明确需转换 | 转 7 段式 Prompt + `AskUserQuestion` 确认 |

### 两段式工作流

```
RAW ──► Step 0 打分 ──► Step 1 转 Prompt ──► Step 2 确认(是/否) ──► Step 3 执行
              │                    │
              └─ 安全区直接答      └─ 用户改完发回即最终指令
```

### 7 段式模板

```prompt
# Role        — 最合适的专家角色
# Goal        — 一句话清晰目标
# Context     — 背景、路径、已知约束
# Constraints — 必须遵守的限制
# Input       — RAW: "用户原话"
# Output Format — 交付物 + 文件路径 + 验证方式
# Steps       — 拆解执行步骤 + 验证步骤
```

## Installation

### Hermes Agent

```powershell
# 用户级（推荐）
Copy-Item SKILL.md $env:LOCALAPPDATA\hermes\skills\prompt-converter\SKILL.md -Force

# 验证
hermes skills list | Select-String prompt-converter
```

### Claude Code (VSCode)

```powershell
# 安装 Skill
mkdir -p $HOME/.claude/skills/prompt-converter
Copy-Item SKILL.md $HOME/.claude/skills/prompt-converter/SKILL.md -Force

# 安装快捷指令 /p（可选）
mkdir -p $HOME/.claude/commands
Copy-Item commands/p.md $HOME/.claude/commands/p.md -Force

# 重启 VSCode 后生效
```

### 快捷触发

```
/p 帮我写个爬虫        # 无视分数，强制转
转prompt 把检测改一下   # 同上
```

## Usage

### Example 1 — 明确任务

**你：** `写个斗鱼弹幕爬虫存到D盘`

**我：**
```
score=0.42 -> 地带: 转换 (原因: 含动词"写"且指向明确交付物)
```prompt
# Role  熟悉斗鱼API的Python工程师
# Goal  实现斗鱼弹幕爬虫并落盘到 D:\
...
```

### Example 2 — 纯问答

**你：** `什么是 MCP`

**我：**
```
score=0.08 -> 地带: 安全 (原因: 纯问答，无交付物)
MCP = Model Context Protocol ...
```
（不转 Prompt，直接回答）

### Example 3 — 灰色地带

**你：** `能不能把这个工程传一下`

**我：**
```
score=0.22 -> 地带: 灰 (原因: 含动词"上传"但无明确目标平台) -> 询问用户
```
`AskUserQuestion`：这算任务(转 Prompt)还是纯问答(直接给建议)？

## Project Structure

```
prompt-converter/
├── SKILL.md          # 核心 Skill（Hermes + Claude Code 通用）
├── commands/
│   └── p.md          # /p 快捷指令（Claude Code）
├── README.md
└── LICENSE (MIT)
```

## Prerequisites

- 无需额外依赖
- Hermes 或 Claude Code 已安装即可

## Verification

```bash
# Hermes
cat $env:LOCALAPPDATA\hermes\skills\prompt-converter\SKILL.md | head -n 5
# Claude Code
cat ~/.claude/skills/prompt-converter/SKILL.md | head -n 5
# GitHub
gh repo view Cthus/prompt-converter --json url
```

每轮输入应先看到 `score=0.xx -> 地带:` 一行，再按地带行为执行。

## License

MIT © Hermes Agent
