# prompt-converter

[![GitHub stars](https://img.shields.io/github/stars/Cthus/prompt-converter?style=social)](https://github.com/Cthus/prompt-converter)
[![Release](https://img.shields.io/github/v/release/Cthus/prompt-converter)](https://github.com/Cthus/prompt-converter/releases/tag/v0.2.0)
[![License](https://img.shields.io/github/license/Cthus/prompt-converter)](./LICENSE)
[![Hermes](https://img.shields.io/badge/Hermes-Skill-blue)](https://github.com/Cthus/prompt-converter)
[![Claude Code](https://img.shields.io/badge/Claude_Code-VSCode-purple)](https://github.com/Cthus/prompt-converter)

**Idioma:** [English](README.en.md) | [日本語](README.ja.md) | [Español](README.es.md) | [中文](README.md)

> Convierte tareas vagas en Prompts estructurados de 7 secciones, editables y revisables. Ejecuta solo tras confirmación. Compatible con Hermes y Claude Code.

Estandariza entradas ambiguas en Prompts reutilizables. Scoring de 3 niveles + confirmación AskUserQuestion.

---

## Resumen

1. Envías una petición粗 (ej. "arregla el detector")
2. Puntúo y convierto a Prompt de 7 secciones
3. Editas y reenvías — ejecuto exactamente eso

Un solo `SKILL.md` funciona en Hermes y Claude Code.

## Cuándo usar

| Trigger | Ejemplo | Comportamiento |
|------|------|------|
| ✅ Tarea | `escribe un crawler` | Convierte + confirma |
| ❌ Pregunta | `qué es MCP` | Responde directo |
| 🔧 Forzar | `/p escribe un crawler` | Convierte sin scoring |

## Scoring

| Puntuación | Zona | Acción |
|------|------|------|
| `0.00 – 0.11` | Segura | Responder directo |
| `0.12 – 0.29` | Gris | Preguntar con AskUserQuestion |
| `≥ 0.30` | Conversión | Convertir a Prompt |

## Instalación

```powershell
Copy-Item SKILL.md $env:LOCALAPPDATA\hermes\skills\prompt-converter\SKILL.md -Force
Copy-Item commands/p.md $HOME/.claude/commands/p.md -Force
```

## Licencia

MIT © Hermes Agent
