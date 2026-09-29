---
summary: "Senpi (OmO Native) usage: pi-mono session roots, OmO task children, and env-var overrides."
ids: [senpi]
read_when:
  - Changing Senpi source detection or watch behavior
  - Debugging missing Senpi usage or un-attributed OmO task sessions
---

# Senpi (OmO Native)

Senpi is a regular Tokscale client (`senpi`), enabled by default on new installs. Existing saved client selections are preserved; enable Senpi in the tracked-tools settings if it is not selected. The widget and headless agent use the same collector. No Senpi credentials or API connection are needed.

## Source

Senpi is a pi-mono descendant. Its transcripts are the same v3 JSONL format as Pi (`version: 3` session headers, `model_change` / `message` entries), but they live under a different agent dir with different env overrides:

- `~/.senpi/agent/sessions/<encoded-cwd>/*.jsonl` — the agent dir honors `SENPI_CODING_AGENT_DIR` (mirror of Pi's `PI_CODING_AGENT_DIR`). The default spelling stays a WSL/source marker only; the watch root is resolved explicitly so an env override redirects it (same pattern as `CODEX_HOME`).
- OmO task children — transcripts spawned as OmO tasks live outside the agent dir. Tokscale reads `SENPI_CODING_AGENT_SESSION_DIR`, the current project's `.omo/senpi-task/children`, and `~/.omo/senpi-task/children`, then recovers every other project's children dir from the `cwd` recorded in global session headers. A `task.state_dir` in `~/.omo/omo.jsonc`/`omo.json` moves the layout again.

Token Monitor watches the resolved agent sessions dir, `SENPI_CODING_AGENT_SESSION_DIR` when set, and the home-default `.omo/senpi-task/children`. Per-project children dirs and a custom `task.state_dir` are not statically watchable; a missing dir is discovered on the next full collection.

## Token accounting

`usage.reasoning` inside a message is a subset of output tokens (not a disjoint bucket), and Tokscale reports the row's `reasoning` as 0 — so no disjoint-reasoning set entry is needed and normalized totals come from the additive components alone.
