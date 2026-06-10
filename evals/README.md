# Evals

These files are development assets for `agent-discussion`. They are intentionally kept outside
`skills/agent-discussion/` so installers receive only the runtime skill.

The eval cases check that the skill:

- treats the workflow as broad peer discussion, not review-only
- chooses or asks for a peer agent instead of assuming a default
- uses persistent multi-round sessions when available
- declines trivial tasks that do not justify a peer loop
- adapts the reasoning style to the task type

Run or adapt these evals from this repository; do not publish them inside the skill folder.
