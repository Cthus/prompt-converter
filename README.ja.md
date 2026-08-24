# prompt-converter

[![GitHub stars](https://img.shields.io/github/stars/Cthus/prompt-converter?style=social)](https://github.com/Cthus/prompt-converter)
[![Release](https://img.shields.io/github/v/release/Cthus/prompt-converter)](https://github.com/Cthus/prompt-converter/releases/tag/v0.2.0)
[![License](https://img.shields.io/github/license/Cthus/prompt-converter)](./LICENSE)
[![Hermes](https://img.shields.io/badge/Hermes-Skill-blue)](https://github.com/Cthus/prompt-converter)
[![Claude Code](https://img.shields.io/badge/Claude_Code-VSCode-purple)](https://github.com/Cthus/prompt-converter)

**Language:** [English](README.en.md) | [日本語](README.ja.md) | [Español](README.es.md) | [中文](README.md)

> 曖昧な自然言語タスクを、編集・レビュー可能な7セクション構造化 Prompt に変換。確認後に実行。Hermes と Claude Code 両対応。

曖昧な入力をレビュー可能で再利用可能な構造化 Prompt に標準化。3段階スコアリング + AskUserQuestion 確認で手戻りを削減。

---

## 概要

1. 粗い依頼を送る（例：「検出器を直して」）
2. スコアリングして 7 セクション Prompt に変換して提示
3. 編集して送り返すと、そのまま実行

1つの `SKILL.md` で Hermes / Claude Code 両方で動作。

## いつ使うか

| トリガー | 例 | 動作 |
|------|------|------|
| ✅ タスク | `弾幕クローラーをDドライブに` | Prompt 変換 + 確認後実行 |
| ❌ Q&A | `MCPとは` | 直接回答 |
| 🔧 強制 | `/p クローラーを書いて` | スコア無視で変換 |

## スコアリング

```
score=0.42 -> zone: 変換
score=0.08 -> zone: 安全
score=0.22 -> zone: グレー -> ユーザーに確認
```

| スコア | ゾーン | 動作 |
|------|------|------|
| `0.00 – 0.11` | 安全 | 直接回答 |
| `0.12 – 0.29` | グレー | AskUserQuestion で確認 |
| `≥ 0.30` | 変換 | 7セクション Prompt に変換 |

## インストール

### Claude Code (VSCode)

```powershell
Copy-Item SKILL.md $HOME/.claude/skills/prompt-converter/SKILL.md -Force
Copy-Item commands/p.md $HOME/.claude/commands/p.md -Force
```

## テンプレート

```prompt
# Role
# Goal
# Context
# Constraints
# Input
# Output Format
# Steps
```

## License

MIT © Hermes Agent
