---
name: feedback-account-separation-strict
description: "Operator wants strict separation between Anthropic accounts — Max OAuth (Claude Code runtime) and Console platform key (orchestrator cloud peers). Architecture must enforce, not rely on memory or discipline."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 05017a5d-eb68-4621-983a-d4f7e91b8fbd
---

Operator said 2026-05-22: "ensure you keep accounts seperate." Context: there are two Anthropic billing surfaces in this stack and they must NOT cross:

1. **Max subscription** — flat-rate $100/mo, used by Claude Code CLI + extension only, authenticated via OAuth at `~/.claude/.credentials.json`.
2. **Platform / Console account** — per-token billing via `sk-ant-` key, used only by CLAF orchestrator's Anthropic peer for Flash escalations.

**Why:** Crossing them silently bills the per-token account for what should be flat-rate Max work. Even when the Console balance is $0 (current state 2026-05-22) the architectural risk persists — a top-up would immediately start bleeding spend through the crossed wire.

**How to apply:**
- Never put the Console `sk-ant-` key under the env name `ANTHROPIC_API_KEY` anywhere a Claude Code launcher might inherit it. Use `ANTHROPIC_CONSOLE_KEY` in `~/.master_ai_keys` and let CLAF's bootstrap alias-project it into its own process env only.
- Every Claude Code launcher MUST `unset ANTHROPIC_API_KEY` before `exec claude`. Verified in `~/projects/claf/launch.sh:30`.
- When wiring new MCP servers or shell helpers that source the keys file, sanity-check that the loaded var is `ANTHROPIC_CONSOLE_KEY`, not the API name.
- When the operator says "fix the crossed env var" or anything similar, do not assume it's a simple unset — verify both directions (keys file source name AND launcher env at exec time) and patch any persistent writers (claf_lockdown.sh) so re-runs don't undo the separation.
- Defense in depth, not single-point fix.

See related: [[project-claf-throttle-and-account-separation]].
