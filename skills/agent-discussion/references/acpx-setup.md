# acpx Setup

Load this reference when `agent-discussion` needs `acpx` but the CLI or skill may be missing.

## Rule

Never install `acpx`, install an `acpx` skill, overwrite an existing skill, or modify user
agent directories without explicit user approval in the current conversation. Show the exact
command and target scope before running it.

## Check First

Use read-only checks:

```bash
command -v acpx
acpx --version
test -f "$HOME/.codex/skills/acpx/SKILL.md" && echo codex-acpx-skill-present
test -f "$HOME/.claude/skills/acpx/SKILL.md" && echo claude-acpx-skill-present
```

Adjust the skill path for the current agent if it is not Codex or Claude Code.

## Install CLI

If the CLI is missing, ask the user before running:

```bash
npm install -g acpx@latest
```

If the user does not want a global install, use `npx acpx@latest` for one-off commands and
say that startup will be slower.

## Install Skill

If the CLI exists but the `acpx` skill is missing, ask the user before running the upstream
installer:

```bash
npx acpx@latest --skill install acpx --agent codex --scope user
```

For Claude Code:

```bash
npx acpx@latest --skill install acpx --agent claude --scope user
```

Use `--scope cwd` only when the user wants a project-local skill. Use `--force` only after
the user explicitly approves overwriting an existing skill.

## Fallback

If setup is declined or unavailable, continue only if the discussion can still be useful
without `acpx`. Otherwise explain that a persistent independent peer session is blocked until
the CLI/skill is installed.
