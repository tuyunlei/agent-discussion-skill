# Codex Adapter

Load this reference only when the selected `acpx` peer is `codex`.

## Session Continuity

- Use `codex prompt -s <topic>` for discussion rounds.
- Do not use `codex exec` for a multi-round discussion; it is one-shot and loses continuity.

## Prompting

- Put the peer stance and role framing in the prompt body. Codex can ignore
  `--append-system-prompt`.
- Keep the shared artifact path and previous-round summary in each follow-up prompt.

## Output

- With `--format quiet`, stdout is the peer's final answer text and token/status details go to
  stderr.
- A non-zero exit means the turn failed.
- `--format json` emits ACP JSON-RPC frames. For `codex-acp`, answer chunks have appeared in
  `session/update` -> `agent_message_chunk.content.text`; do not assume a generic
  `{"type":"result"}` schema.
- Codex replies may start with a multi-byte character or a very long first line. Read `.out`
  files whole with `cat` or an equivalent full-file read, not `head`, `sed`, or byte previews.

## Liveness

If you need liveness, use an `acpx` status/history command for the same `--cwd`. Do not infer
failure from an empty redirected output file mid-turn.
