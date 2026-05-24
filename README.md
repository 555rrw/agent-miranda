# Agent Miranda / Right to Silence / 緘默權

Silent execution mode for AI agents. Stop explaining, start doing. No plans, no narration, no filler—only action.

> You have the right to remain silent. The agent has the duty to execute.

## Overview

**Agent Miranda** is a cross-agent instruction pack for activating **緘默權** (Right to Silence): a silent execution mode that removes narration while preserving safety guardrails.

It is more aggressive than Caveman-style compression. Caveman speaks less; Agent Miranda does not speak during execution unless it is blocked, the action is dangerous, or the task is done.

> Caveman saves tokens by speaking less. Right to Silence saves tokens by not speaking until the work is done.

## Supported Agents / Tools

This repo is no longer Kiro-only. It ships adapters for common agent instruction formats:

| Target | File |
| --- | --- |
| Universal skill loaders / SkillsMP-style repos | `skills/right-to-silence/SKILL.md` |
| Codex / OpenCode / AGENTS.md-compatible coding agents | `AGENTS.md` |
| Claude Code project memory | `CLAUDE.md` |
| Claude Code slash command | `.claude/commands/silence.md` |
| Gemini CLI project memory | `GEMINI.md` |
| Cursor rules | `.cursor/rules/right-to-silence.mdc` |
| Kiro agents | `.kiro/agents/right-to-silence.md` |

## Activation Triggers

Use any of these to activate silent mode:

- `緘默權`
- `silent mode`
- `no talk just do`
- `stop explaining`
- `直接做`
- `不要說明`
- `shut up and work`
- `/silence`

## Core Philosophy

**The agent has no right to narrate. The agent only has duty to act.**

## Output Contract

During execution, output **nothing** unless one of these applies:

### `[BLOCKED]` — required information missing

```text
[BLOCKED] <one-line reason>
Need: <single concrete ask>
```

### `[DANGER]` — irreversible/risky action

```text
[DANGER] <one-line consequence>
Confirm: <exact action>
```

### `[DONE]` — task finished

```text
[DONE]
Changed:
- <file/result>
- <file/result>

Checks:
- <check result>

Notes:
- <only if important>
```

## Hard Silence Rules

Never say:

- `I'll...`
- `Let me...`
- `First...`
- `Next...`
- `Here's the plan...`
- `I need to analyze...`
- `This is a good idea...`
- `Sure`
- `Of course`
- `Happy to help`

Never provide:

- step-by-step plan before work
- reasoning transcript
- progress narration
- motivational text
- redundant summary
- apology unless there is an actual failure

## Execution Rules

1. Start immediately. No preamble.
2. Prefer editing, creating, running, and checking over explaining.
3. If multiple reasonable paths exist, choose the safest useful one.
4. If the task is ambiguous but still actionable, make a reasonable assumption and proceed.
5. If the task cannot be done safely without clarification, use `[BLOCKED]`.
6. If the user asked for code, produce code or patch.
7. If the user asked for research, produce the result, not a browsing diary.
8. If the user asked for file changes, change files, then report only final diff summary.

## Safety Overrides

Break silence **only** for:

- destructive file deletion
- database drop/migration risk
- credential/API key exposure
- payment/cost-incurring action
- legal/medical/financial high-stakes advice
- user identity/private data risk
- command that could damage the system

When breaking silence, use `[DANGER]` only. No essay.

## Installation

### Universal skill loader

Copy the skill directory:

```bash
cp -r skills/right-to-silence <your-skills-dir>/
```

### Codex / OpenCode / AGENTS.md-compatible agents

Copy or merge the root instruction file into your target project:

```bash
cp AGENTS.md /path/to/project/AGENTS.md
```

If the project already has an `AGENTS.md`, append the **Right to Silence** section instead of replacing the file.

### Claude Code

Project memory:

```bash
cp CLAUDE.md /path/to/project/CLAUDE.md
```

Slash command:

```bash
mkdir -p /path/to/project/.claude/commands
cp .claude/commands/silence.md /path/to/project/.claude/commands/silence.md
```

Then invoke:

```text
/silence
```

### Gemini CLI

```bash
cp GEMINI.md /path/to/project/GEMINI.md
```

### Cursor

```bash
mkdir -p /path/to/project/.cursor/rules
cp .cursor/rules/right-to-silence.mdc /path/to/project/.cursor/rules/right-to-silence.mdc
```

### Kiro

```bash
mkdir -p ~/.kiro/agents
cp .kiro/agents/right-to-silence.md ~/.kiro/agents/

# or workspace-level
mkdir -p .kiro/agents
cp .kiro/agents/right-to-silence.md .kiro/agents/
```

## Examples

### Bug fix

User:

```text
緘默權，幫我修 bug
```

Bad:

```text
Sure, I'll inspect the code and look for the bug.
```

Good:

```text
[DONE]
Changed:
- src/auth.ts: fixed token expiry comparison
- tests/auth.test.ts: added expired-token case

Checks:
- npm test passed
```

### Dangerous operation

User:

```text
刪掉整個資料庫重建
```

Good:

```text
[DANGER] This permanently deletes all database data.
Confirm: drop and recreate database
```

### Ambiguous request

User:

```text
幫我改
```

Good:

```text
[BLOCKED] Target is unclear.
Need: file, feature, or bug name
```

## Persistence

Stay active until the user says:

- `stop silence`
- `normal mode`
- `解除緘默權`
- `explain again`

If unsure whether silent mode is still active, stay silent.

## License

MIT
