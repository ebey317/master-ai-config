---
name: Claude → Sensei memory bridge
description: ~/scripts/sync_hard_limits.py propagates Claude Code's feedback_*.md (as RULES) and project_*.md (as PROJECT MEMORY) from ~/.claude/projects/-home-elijah/memory/ into Sensei's flat ~/.master_ai_memory between auto-managed markers. Idempotent. Sensei reads its memory file every turn, so anything saved on Claude's side flows into Sensei after a sync. Resume flow is shared too: carryover doc first, then newest AI_CONTEXT snapshot.
type: project
originSessionId: 699e802a-fbc2-435a-8beb-e7f026c2f3ff
---
`~/scripts/sync_hard_limits.py` is the bridge between Claude Code's memory store and Sensei's runtime memory.

**Why:** Sensei used to live in a separate behavioral universe — Claude Code's feedback (rules, gotchas, preferences) and project state (architecture decisions, ongoing threads) didn't reach her. Elijah wanted both AIs following the same guardrails AND carrying the same project context, so when he mentions a project to Sensei she can surface the same thread Claude can.

**How to apply:**
- **Source:** `~/.claude/projects/-home-elijah/memory/feedback_*.md` (becomes RULES) + `project_*.md` (becomes PROJECT MEMORY).
- **Destination:** `~/.master_ai_memory` (Sensei's flat memory file, read every turn).
- **Marker block:** content lives between `<<< CLAUDE-SYNC HARD LIMITS — auto-managed, do not hand-edit >>>` and `<<< END CLAUDE-SYNC >>>`. Hand-edited content above/below the markers is preserved verbatim.
- **Default mode:** compact — uses `name:` + `description:` from each memory's frontmatter. Project memories ALWAYS compact (full bodies would balloon Sensei's per-turn context). Pass `--full` for verbose feedback bodies (rules only).
- **Run modes:** `python3 ~/scripts/sync_hard_limits.py` (dry-run preview) or `--write` (actually write). Idempotent — re-running replaces the marked block, never duplicates.

**Practical implications:**
- When you save a feedback memory here, it can flow into Sensei the next time `sync_hard_limits.py --write` runs. Memory writes have downstream reach now, not just future Claude sessions.
- Project memory `description:` fields are load-bearing — they're literally what Sensei sees as the headline of each project. Keep descriptions specific.
- The script is **untracked in git** as of 2026-04-25 — sitting in `~/scripts/` but not committed. Not yet wired into Sensei's startup or `refresh`; Elijah runs it manually.
- The two memory stores are NOT independent. Future-Claude shouldn't assume Sensei's `~/.master_ai_memory` is a separate write surface — it's downstream of this directory.
- Resume flow is shared too: when `feedback_where_were_we.md` says to read the carryover doc first, then the newest AI_CONTEXT snapshot, Sensei should follow that same order after sync.
