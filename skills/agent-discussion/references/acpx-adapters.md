# acpx Adapter Index

Load this reference when `agent-discussion` will run a peer through `acpx`.
Keep agent-specific behavior in the per-adapter files below, not in this index.

If the `acpx` CLI or `acpx` skill may be missing, read `acpx-setup.md` before using these
commands.

## Common Pattern

```bash
TOPIC=short-topic-name
PEER=chosen-agent
WORK="${XDG_CACHE_HOME:-$HOME/.cache}/agent-discussion/$TOPIC"
mkdir -p "$WORK"

# If round1-prompt.md already exists, the topic name was reused. Add a date suffix instead
# of overwriting another discussion.

acpx --cwd "$WORK" "$PEER" sessions ensure --name "$TOPIC"

acpx --cwd "$WORK" --approve-all --prompt-retries 2 --timeout 1800 --format quiet \
  "$PEER" prompt -s "$TOPIC" --file "$WORK/round1-prompt.md" \
  > "$WORK/round1-reply.out" 2> "$WORK/round1-reply.err"
```

Run every later round with the same `PEER`, `TOPIC`, and `WORK`.

## Choosing An Adapter

- User names an agent: use it.
- User does not name one: pick a coding agent different from the current executor when
  available.
- If no different coding agent is available, ask the user or explain the limitation.
- Use `acpx` adapter names literally, such as `codex`, `claude`, or `gemini`.
- After selecting the peer, read the matching adapter reference when it exists:
  - `adapters/codex.md` for `codex`
  - `adapters/claude.md` for `claude`
- If no matching adapter reference exists, rely only on the common pattern above and the
  general `acpx` skill.

## Common Output Pitfall

When `acpx` stdout is redirected, the output file may remain empty until the peer turn
finishes. This is normal. Do not retry because a file looks empty. Start one blocking command,
wait for it to return, then inspect exit code and full output.

## Work Directory Pitfall

Never use shared prompt paths like `/tmp/r1_prompt.md`. A stale prompt from another session can
be read by `acpx` and produce a high-quality answer to the wrong topic. Use a semantic
per-discussion cache directory such as `$XDG_CACHE_HOME/agent-discussion/<topic>` or another
isolated path.
