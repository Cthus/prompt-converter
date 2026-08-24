# prompt-converter

把随口一句任务，转换成可编辑的 7 段式结构化 Prompt，确认后再执行。Hermes + Claude Code 双端通用。

## 特性

- **每轮必带 score**：`0.00-0.11 安全区(直接答) / 0.12-0.29 灰色地带(必须 AskUserQuestion) / ≥0.30 转换区(转 Prompt)`
- **两段式工作流**：你发粗话 → 我转 Prompt → 你改完发回 → 我按改版执行
- **AskUserQuestion 确认**：转换后弹「确认执行？是(推荐)/否」
- **双端通用**：同一份 `SKILL.md` 可装到 Hermes 和 Claude Code (VSCode)

## 阈值

| 分数 | 地带 | 行为 |
|------|------|------|
| 0.00-0.11 | 安全区 | 直接回答，不转 |
| 0.12-0.29 | 灰色 | 必须反问：任务还是问答？ |
| ≥0.30 | 转换区 | 转 7段式 Prompt + 确认 |

## 安装

### Hermes
```powershell
# 复制 skill
Copy-Item SKILL.md $env:LOCALAPPDATA\hermes\skills\prompt-converter\SKILL.md -Force
```

### Claude Code (VSCode)
```powershell
mkdir -p $HOME/.claude/skills/prompt-converter
Copy-Item SKILL.md $HOME/.claude/skills/prompt-converter/SKILL.md -Force
mkdir -p $HOME/.claude/commands
Copy-Item commands/p.md $HOME/.claude/commands/p.md -Force
# 重启 VSCode
```

### 强制触发
```
/p 帮我写个斗鱼弹幕爬虫存到D盘
```

## 7 段式模板

```prompt
# Role
# Goal
# Context
# Constraints
# Input
# Output Format
# Steps
```

## 演示

- `写个斗鱼弹幕爬虫` → `score=0.42 -> 转换` → 出 Prompt
- `什么是MCP` → `score=0.08 -> 安全区` → 直接答
- `能不能上传这个工程` → `score=0.22 -> 灰` → 询问平台

## License

MIT
