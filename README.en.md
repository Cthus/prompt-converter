# prompt-converter

[![GitHub stars](https://img.shields.io/github/stars/Cthus/prompt-converter?style=social)](https://github.com/Cthus/prompt-converter)
[![Release](https://img.shields.io/github/v/release/Cthus/prompt-converter)](https://github.com/Cthus/prompt-converter/releases/tag/v0.2.0)
[![License](https://img.shields.io/github/license/Cthus/prompt-converter)](./LICENSE)
[![Hermes](https://img.shields.io/badge/Hermes-Skill-blue)](https://github.com/Cthus/prompt-converter)
[![Claude Code](https://img.shields.io/badge/Claude_Code-VSCode-purple)](https://github.com/Cthus/prompt-converter)

**Language:** [English](README.en.md) | [日本語](README.ja.md) | [Español](README.es.md) | [中文](README.md)

> Transform vague natural language tasks into editable, reviewable 7-section structured Prompts. Execute only after confirmation. Works with both Hermes and Claude Code.

Standardize ambiguous natural language input into reviewable, reusable structured Prompts. Built-in 3-tier scoring + AskUserQuestion confirmation to reduce ambiguity and rework.

---

## Overview

`prompt-converter` solves a common collaboration pain: user input is short and underspecified, models guess wrong. With it:

1. You send a rough request (e.g. "fix the detector")
2. I score it and convert to a 7-section Prompt for review
3. You edit and send it back — I execute exactly as revised, no second guessing

One `SKILL.md` works for both **Hermes Agent** and **Claude Code (VSCode)** with zero changes.

## When to Use

| Trigger | Example | Behavior |
|------|---------|----------|
| ✅ Task / Action | `write a danmaku crawler to D drive` `push this Skill to GitHub` | Convert to Prompt + confirm then execute |
| ❌ Q&A | `what is MCP` `difference between A and B` | Answer directly, no conversion |
| ❌ Chit-chat | `hello` `is that what you mean?` | Answer directly |
| 🔧 Force | `/p fix the detector` | Convert regardless of score |

> Exception: if current message is already a structured Prompt (contains `Role/Goal/Constraints`) or explicit approval (`ok`/`yes`), skip conversion and execute.

## How it Works

### Intelligent Scoring — Every Reply Includes a Score

Every input shows a score line for transparency:

```
score=0.42 -> zone: convert (reason: contains verb "write" + clear deliverable "crawler")
score=0.08 -> zone: safe (reason: pure Q&A, no deliverable)
score=0.22 -> zone: gray (reason: contains verb "upload" but no target platform) -> ask user
```

| Score | Zone | Behavior |
|------|------|----------|
| `0.00 – 0.11` | Safe | Answer directly |
| `0.12 – 0.29` | Gray | Must ask via `AskUserQuestion`: "Task or Q&A?" |
| `≥ 0.30` | Convert | Convert to 7-section Prompt + confirm |

### Two-Stage Workflow

```
RAW ──► Step 0 Score ──► Step 1 Convert ──► Step 2 Confirm(yes/no) ──► Step 3 Execute
              │                    │
              └─ safe → answer     └─ edited Prompt is final instruction
```

### 7-Section Template

```prompt
# Role        — best-fit expert role
# Goal        — one-line clear goal
# Context     — background, paths, constraints
# Constraints — hard limits
# Input       — RAW: "original message"
# Output Format — deliverables + paths + verification
# Steps       — execution + verification steps
```

## Installation

### Hermes Agent

```powershell
Copy-Item SKILL.md $env:LOCALAPPDATA\hermes\skills\prompt-converter\SKILL.md -Force
hermes skills list | Select-String prompt-converter
```

### Claude Code (VSCode)

```powershell
mkdir -p $HOME/.claude/skills/prompt-converter
Copy-Item SKILL.md $HOME/.claude/skills/prompt-converter/SKILL.md -Force
mkdir -p $HOME/.claude/commands
Copy-Item commands/p.md $HOME/.claude/commands/p.md -Force
# Restart VSCode
```

### Force Trigger

```
/p write a crawler
/prompt fix the detector
```

## Usage

**You:** `write a danmaku crawler to D drive`  
**Me:** `score=0.42 -> zone: convert` + 7-section Prompt

**You:** `what is MCP`  
**Me:** `score=0.08 -> zone: safe` + direct answer (no conversion)

**You:** `can you upload this project`  
**Me:** `score=0.22 -> zone: gray` + AskUserQuestion

## Project Structure

```
prompt-converter/
├── SKILL.md
├── commands/p.md
├── README.md / README.en.md / README.ja.md / README.es.md
└── LICENSE (MIT)
```

## License

MIT © Hermes Agent
