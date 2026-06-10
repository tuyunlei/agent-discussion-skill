# agent-discussion

`agent-discussion` is a Skill for running a multi-round peer discussion with a second
independent coding agent. It is broader than code review: use it for designs, debugging,
root-cause analysis, tradeoff decisions, claim verification, planning, and other non-trivial
questions where an independent peer can reduce single-model blind spots.

## Install

List the published skill:

```bash
npx skills add tuyunlei/agent-discussion-skill --list
```

Install only the runtime skill:

```bash
npx skills add tuyunlei/agent-discussion-skill --skill agent-discussion
```

## Repository Layout

```text
skills/agent-discussion/   # Published runtime skill
evals/                     # Development eval cases, not installed as the skill
```

Keep these concerns separate:

- Runtime skill contents live under `skills/agent-discussion/`.
- Evals are kept in the repository so behavior can be tested and improved over time.
- Do not copy `evals/` into the published skill directory.

## acpx

This skill can use `acpx` to drive peer coding agents. It does not silently install `acpx` or
any agent skill. If the CLI or `acpx` skill is missing, the skill instructs the agent to ask
the user before running installation commands.
