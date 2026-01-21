# GitHub Copilot Agent Configuration

This repository contains my personal configuration for GitHub Copilot Agent in VS Code. \
It can serve as a useful starting point for those new to GitHub Copilot in VS Code. \
These instructions are designed to help achieve effective responses to zero-shot prompting during typical development tasks.

<img width="568" height="507" alt="example" src="https://github.com/user-attachments/assets/8e174441-7961-4ce9-b150-d66c2d4f4056" />

## Overview

- `.github`
  - [.github/agents](.github/agents): Custom Agents
  - [.github/instructions](.github/instructions): Custom Instructions
  - [.github/skills](.github/skills): Agent Skills
  - [.github/copilot-instructions.md](.github/copilot-instructions.md): Workspace-optimized instructions
- `.vscode`
  - [.vscode/mcp.json](.vscode/mcp.json): Default MCP server configuration
  - [.vscode/settings.json](.vscode/settings.json): Default VS Code settings

## Environment

- **VS Code**: Version 1.108.x
- Required MCP commands: `npx`, `docker`, `uvx`
- Tested models: `Claude Opus 4.5`, `Claude Sonnet 4.5`, `GPT-5.2`

> **Note**: Behavior may vary depending on the model. Each model interprets instructions and context differently.

## Configuration Files

### Custom Instructions

- [copilot-agent.instructions.md](.github/instructions/copilot-agent.instructions.md): 6-phase execution protocol with SSOT hierarchy, quality standards, and no-assumption rules
- [subagents.instructions.md](.github/instructions/subagents.instructions.md): SSOT defining forbidden MCP calls, delegation rules, and Context Package contract
- [taming-copilot.instructions.md](.github/instructions/taming-copilot.instructions.md): User directive priority, code-on-request-only, and direct/concise response philosophy
- [task-direction-approval.instructions.md](.github/instructions/task-direction-approval.instructions.md): Mandatory user consent before tech/architecture changes with failure-to-options protocol

### Agent Skills

- [investigation-mode](.github/skills/investigation-mode/SKILL.md): Pause implementation after 2+ consecutive failures, switch to root-cause analysis before resuming
- [minimalist-surgical-development](.github/skills/minimalist-surgical-development/SKILL.md): Smallest diff first, standard libraries preferred, no unsolicited refactoring
- [root-cause-tracing](.github/skills/root-cause-tracing/SKILL.md): Trace bugs backward through call stack to find the original trigger, not the symptom
- [task-direction-approval](.github/skills/task-direction-approval/SKILL.md): Explain failure cause, present 2-3 options with trade-offs, wait for explicit user choice
- [uncertainty-verification](.github/skills/uncertainty-verification/SKILL.md): Verify exact commands/configs/APIs via official docs before stating - no assumption-based specifics
- [verification-before-completion](.github/skills/verification-before-completion/SKILL.md): Run verification commands and confirm output before any completion claims - evidence before assertions

### Custom Agents

- [critical-thinking.agent.md](.github/agents/critical-thinking.agent.md): Question assumptions and guide toward optimal solutions
- [mentor.agent.md](.github/agents/mentor.agent.md): Support engineer growth through critical questioning and mentorship

# MetaPrompts

This repo contains GitHub Copilot customization files meant to be copied into your own repo, centered on the meta agent `ai-builder`.

## Use the meta agent in VS Code

1. Copy the .github folder into your repo.
2. Open your repo in VS Code.
3. Open the Copilot Chat view.
4. Select the agent `ai-builder` from the agent picker, or type @ai-builder in the chat.
5. Ask for help creating or maintaining Copilot customizations.

Example requests:
- Create a new agent for database schema design.
- Validate my agent and skill files.
- Generate documentation for the current agents.

## Meta agent capabilities

The `ai-builder` agent can:

- Design and create agents, skills, prompts, and instructions.
- Recommend the right customization type for a request (agent vs prompt vs instructions vs workflow).
- Build multi-agent workflows with handoffs and review points.
- Validate and troubleshoot customization files for format issues.
- Analyze overlaps and redundancies across agents, skills, prompts, and instructions.
- Generate documentation and usage guides for your customizations.

## What to copy

- [​.github/agents/ai-builder.md](.github/agents/ai-builder.md) — the meta agent definition
- [​.github/skills](.github/skills) — reusable procedures referenced by agents
- [​.github/prompts](.github/prompts) — reusable prompt templates
- [​.github/instructions](.github/instructions) — auto-applied instructions by file pattern

## Notes

- Keep reusable procedures in skills and reference them from agents.
- Add project-wide guidance in instructions so it applies automatically.
- Commit changes to share the configuration across the team.
