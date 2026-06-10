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
