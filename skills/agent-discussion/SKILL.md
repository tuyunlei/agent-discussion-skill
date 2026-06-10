---
name: agent-discussion
description: >-
  Discuss any non-trivial question, plan, design, diagnosis, decision, or analysis with a
  second independent coding agent over multiple peer rounds. Use when the user asks for
  agent-discussion, agent-review, another/second/independent agent, or asks to discuss with
  a specific agent such as Codex, Claude, or Gemini.
---

# agent-discussion

Use this skill to bring in a second independent coding agent for a real discussion. Despite
the legacy trigger `agent-review`, the job is broader than review: design critique,
debugging, root-cause analysis, tradeoff decisions, claim verification, planning, and any
other non-trivial topic where a second agent can reduce single-model blind spots.

The skill governs discussion mechanics, not the subject matter. Choose the reasoning style
from the task: evidence-driven debugging, option tradeoffs, adversarial review, source
verification, or another discipline that fits the question.

## When To Use

- Use it when the user explicitly asks for `agent-discussion`, `agent-review`, another
  agent, an independent agent, or discussion with a named coding agent.
- If the request is trivial, say it does not justify a multi-agent loop and handle it
  directly.
- If the user only wants your answer, do not add a second agent on your own.

## Pick The Peer

- If the user names an agent, use that agent.
- If the user does not name one, choose a coding agent different from the current executor
  when available.
- If the best peer is not obvious or tool availability is unclear, ask one short question.
- Do not describe any agent as the default. State the selected peer and why.
- Prefer a persistent multi-turn peer session. If the selected agent cannot support one,
  say so and either choose another peer or explain the reduced value before proceeding.

## Discussion Loop

1. **Write the shared artifact first.** Put the topic into a markdown doc and give the user
   the absolute path. Every round should refer to that artifact. Keep prompts, replies, and
   notes under a semantic per-discussion work directory. Prefer an OS-appropriate cache
   directory such as `$XDG_CACHE_HOME/agent-discussion/<topic>` on Linux or
   `~/Library/Caches/agent-discussion/<topic>` on macOS.
2. **Open one persistent peer session.** Use the same session and same `cwd` for every round,
   including the first. The first round asks for a cold skeptical pass based on source
   evidence, not memory.
3. **Brief the peer precisely.** Include role, task, shared artifact path, relevant files or
   sources, what to inspect first, what kind of feedback is wanted, and expected output shape.
4. **Research independently between rounds.** Read sources, run checks, or inspect data
   yourself. Do not act as a relay for the peer.
5. **Integrate honestly.** Accept valid objections, reject weak ones with reasons, and keep
   unresolved disagreements visible.
6. **Iterate until convergence or a useful disagreement.** Stop only when the peer signs off
   and you hold no independent objection, or when the disagreement itself is the result the
   user needs.
7. **Update the artifact and hand off.** The output is a discussed artifact plus the remaining
   decision points. The human remains final arbiter.

## Prompt Contract

Every peer prompt should include:

- **Peer stance:** "We are two AI coding agents working as peers. Your job is to find weak
  assumptions, missed evidence, bad tradeoffs, and anything I got wrong."
- **Research mandate:** tell the peer what to read or verify before reasoning.
- **Exact ask:** blind spots, root cause, option comparison, claim verification, sign-off, or
  another concrete round objective.
- **Output shape:** verdict, ranked findings, evidence, suggested direction, and open
  questions.
- **Continuity:** include the shared artifact path and the previous-round summary after round
  one.

## Mechanics

Use `acpx` when it is available, because it can drive multiple coding agents through a
consistent CLI. For general `acpx` command behavior, use the `acpx` skill. Read
`references/acpx-setup.md` first if the `acpx` CLI or `acpx` skill may be missing. Read
`references/acpx-adapters.md` before running `acpx`, choosing a specific adapter, or relying
on adapter-specific behavior.

Stable mechanics regardless of adapter:

- Write long prompts to files under the discussion work directory and pass them with
  `--file`.
- Run each peer call as one blocking command with a generous timeout; then read the complete
  output file and exit code.
- Keep all intermediate prompts and replies in the discussion work directory, not the user's
  repo.
- Do not poll redirected output files as a liveness check.
- Do not use one-shot execution for a multi-round discussion when a persistent session is
  available.

## Guardrails

- Do not fabricate behavior, facts, or source evidence. Verify before asserting.
- Do not manufacture consensus. Two agents can converge on the same wrong answer.
- Do not overfit discussion style. Debugging is not architecture review; selection is not bug
  hunting; verification is not redesign.
- Do not bury adapter details in the main workflow. Put fragile agent-specific behavior in
  references and load it only when needed.
