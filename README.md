<div align="center">

# agent-skills

**Skill management utilities — create, evaluate, install, and compose Agent Skills**

[![GitHub](https://img.shields.io/badge/github-full--stack--skills%2Fagent--skills-green.svg)](https://github.com/full-stack-skills/agent-skills)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Compatible-purple.svg)](https://agentskills.io)

English | [简体中文](./README.zh-CN.md)

</div>

---

## 📖 Introduction

**Agent Skills** provides the complete toolchain for working with Agent Skills — from creation and evaluation to installation and workflow composition. Part of the [Full Stack Skills](https://github.com/partme-ai/full-stack-skills) ecosystem maintained by [PartMe.AI](https://github.com/partme-ai).

## 📦 Install

```bash
npx skills add full-stack-skills/agent-skills
```

## 🎯 Skills (11)

| Skill | Description |
|-------|-------------|
| `agent-browser` | Browser automation CLI for AI agents. Install, commands, selectors, sessions, CDP/streaming. |
| `code-generator` | Code generation guidance — template-based generation, scaffolding, and automated code creation. |
| `codegraph` | Semantic code intelligence for AI coding agents based on tree-sitter knowledge graph. Query code structure, symbol dependencies, call chains, and change impact. 20+ languages, 100% local. |
| `mcp-builder` | Guide for creating high-quality MCP (Model Context Protocol) servers. Python FastMCP / TypeScript SDK. |
| `skill-awesome` | The canonical knowledge base for designing Agent Skills. Contains distilled Agent Skills specifications. |
| `skill-creator` | Guide for creating effective skills. Use when users want to create a new skill or update an existing one. |
| `skill-installer` | Manages the installation and discovery of AI skills from the PartMe marketplace. |
| `skill-official-evaluation` | Official evaluation framework for Agent Skills quality assessment. |
| `skill-sop-creator` | Guide for creating Standard Operating Procedures and composite workflows by combining atomic Agent Skills. |
| `skill-trace-checker` | Checker for TRACE evaluation reports and skill quality validation. |
| `skill-trace-evaluation` | TRACE 五维度评测（T/R/A/C/E），输出 Markdown/HTML 报告与雷达图。 |


## 🤖 Supported Agents

Works with [Claude Code](https://code.claude.com), [Codex](https://developers.openai.com/codex), [Cursor](https://cursor.com), [OpenCode](https://opencode.ai), [Gemini CLI](https://geminicli.com), [GitHub Copilot](https://github.com/features/copilot), [Windsurf](https://codeium.com/windsurf), and [70+ others](https://agentskills.io/clients).

### Claude Code Installation

**Option 1: npx skills CLI (Recommended)**

```bash
npx skills add full-stack-skills/agent-skills
```

**Option 2: Manual Installation**

```bash
git clone https://github.com/full-stack-skills/agent-skills.git
cp -r agent-skills/skills/* .claude/skills/
```

For more details, see the [Claude Code Skills Guide](https://code.claude.com/docs/en/skills) and [Agent Skills Spec](https://agentskills.io/).

## 📄 License

Apache 2.0 — see [LICENSE](LICENSE).
