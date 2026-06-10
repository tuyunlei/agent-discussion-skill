# Claude Adapter

Load this reference only when the selected `acpx` peer is `claude`.

## Peer Stance

Consider `--append-system-prompt` to set the peer stance once for the session:

```bash
acpx --cwd "$WORK" --append-system-prompt \
  "You are a peer coding agent. Do not rubber-stamp; disagree when evidence supports it." \
  claude sessions ensure --name "$TOPIC"
```

Still include the round-specific ask, evidence mandate, shared artifact path, and output shape
in each prompt file.

## Recursion Risk

If the current executor is Claude, do not choose `claude` as the peer unless the user
explicitly requested it and understands that this may reduce model diversity. Prefer a
different coding agent when available.

## Startup Bound

Claude adapter startup can fail or hang before a session is created. Treat session creation as
part of the testable peer-reachability check:

- If `claude sessions ensure` does not return in a reasonable bounded attempt, stop waiting.
- Check `acpx --cwd "$WORK" claude status` and `sessions list --local`.
- If no Claude session or PID is visible, report that Claude was not reached through `acpx`
  rather than retrying indefinitely.
- Clean up only processes scoped to the discussion work directory if a startup attempt left
  dangling commands.

## Spawn Diagnostics

Separate these two failure layers:

- If stderr only shows `spawning built-in agent ... npm exec ... claude-agent-acp` and never
  reaches `initialized protocol version`, the ACP adapter process has not started yet. Treat
  this as an npm package install/exec bridge stall, not as a Claude session failure. Do not
  loop; ask before installing or updating adapter packages.
- If stderr reaches `initialized protocol version` and then `session/new` fails with
  `spawn Unknown system error -88`, the adapter was reached but failed while spawning Claude.
  First verify the real Claude CLI works with `claude -p 'Return exactly OK.'`.

When the real Claude CLI works but the adapter reports `spawn Unknown system error -88`,
prefer passing the installed Claude executable explicitly:

```bash
CLAUDE_CODE_EXECUTABLE="$(command -v claude)" \
  acpx --cwd "$WORK" claude sessions ensure --name "$TOPIC"
```

This avoids relying on the Claude binary bundled inside the adapter's SDK dependency. If the
same command still stalls before protocol initialization, the remaining problem is adapter
package startup; use a bounded attempt and ask the user before changing package versions or
installing a pinned adapter.
