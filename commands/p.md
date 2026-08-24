---
name: p
description: 将本条输入强制转为结构化 Prompt
---

将用户的输入 $ARGUMENTS 视为 RAW，无视智能识别分数，强制按 prompt-converter Skill 的 Step 1 模板转为 7 段式 Prompt：

```prompt
# Role
# Goal
# Context
# Constraints
# Input
# Output Format
# Steps
```

填好后等待用户修改，收到修改版后直接执行。
