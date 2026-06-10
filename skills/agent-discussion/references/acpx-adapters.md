# acpx Adapter Notes

Load this reference when `agent-discussion` will run a peer through `acpx`.
Keep adapter-specific behavior here, not in the main skill.

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

## Claude Adapter

- Consider `--append-system-prompt` to set the peer stance once for the session:

```bash
acpx --cwd "$WORK" --append-system-prompt \
  "You are a peer coding agent. Do not rubber-stamp; disagree when evidence supports it." \
  "$PEER" sessions ensure --name "$TOPIC"
```

- Still include the round-specific ask and evidence mandate in each prompt file.

## Codex Adapter

Use these notes only when the selected peer is `codex`.

- `codex prompt -s <topic>` keeps continuity; `codex exec` is one-shot and loses the
  multi-round discussion state.
- With `--format quiet`, stdout is the peer's final answer text and token/status details go
  to stderr. A non-zero exit means the turn failed.
- `--format json` emits ACP JSON-RPC frames. For `codex-acp`, answer chunks have appeared in
  `session/update` -> `agent_message_chunk.content.text`; do not assume a generic
  `{"type":"result"}` schema.
- Codex can ignore `--append-system-prompt`; put peer stance and role framing in the prompt
  body.
- Codex replies may start with a multi-byte character or a very long first line. Read `.out`
  files whole with `cat` or an equivalent full-file read, not `head`, `sed`, or byte previews.
- If you need liveness, use an `acpx` status/history command for the same `--cwd`; do not infer
  failure from an empty redirected output file mid-turn.

## Output Buffering Pitfall

When `acpx` stdout is redirected, the output file may remain empty until the peer turn
finishes. This is normal. Do not retry because a file looks empty. Start one blocking command,
wait for it to return, then inspect exit code and full output.

## Work Directory Pitfall

Never use shared prompt paths like `/tmp/r1_prompt.md`. A stale prompt from another session can
be read by `acpx` and produce a high-quality answer to the wrong topic. Use a semantic
per-discussion cache directory such as `$XDG_CACHE_HOME/agent-discussion/<topic>` or another
isolated path.
