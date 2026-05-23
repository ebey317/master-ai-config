---
name: "Where were we" = read the newest AI_CONTEXT snapshot
description: 2026-04-23 rule (replaces the retired brainstorm-doc pointer). On "where were we" / catch me up / read me in, open `~/Desktop/CARRYOVER_2026-04-25_sensei_io_split.md` first when present, then the NEWEST file matching `~/Desktop/AI_CONTEXT/context_*.txt` (auto-saved every 5 min by `~/scripts/save_context.sh` via cron) and present it. Shared flow for Claude + Sensei. Check `~/Desktop/master_ai_project_notes.md` for archived brainstorm residue when relevant. Do NOT look for `~/Desktop/master_ai_brainstorm_*.md` — that file was retired 2026-04-22.
type: feedback
originSessionId: f113ec3f-cd82-473e-9850-b39193a31d14
---
**The rule:** Elijah's canonical phrase is **"where were we"**. On that trigger, open and present the NEWEST file matching `~/Desktop/AI_CONTEXT/context_*.txt`. Don't re-synthesize from memory.

**2026-04-25 explicit save point (read FIRST, then the AI_CONTEXT snapshot):** `~/Desktop/CARRYOVER_2026-04-25_sensei_io_split.md` — Elijah's explicitly designated resume point for the Sensei input/output split build (option 1, two-pane tmux split, phased over 4 phases). Companion full-snapshot tarball at `~/Desktop/MASTER_AI_SNAPSHOT_2026-04-25.tar.gz` (57 MB, 197 files, scripts + memory + key Desktop docs). On "where were we," open the carryover doc first for the immediate landing, then the newest AI_CONTEXT snapshot for filesystem freshness. The carryover doc supersedes ad-hoc memory recall for what's next.

**Shared flow:** Claude and Sensei must use that same resume order. No alternate recap path, no memory-only re-synthesis, no skipping the carryover doc when it exists.

Secondary phrasings he uses (treat the same way): *"catch me up," "where are we," "what's the story," "read me in."*

**Why:** The brainstorm doc (`~/Desktop/master_ai_brainstorm_2026-04-22.md`) was retired on 2026-04-22. Its residue lives in `~/Desktop/master_ai_project_notes.md` now. The live "where were we" artifact is the auto-snapshot system at `~/Desktop/AI_CONTEXT/` — a cron job (`*/5 * * * * bash ~/scripts/save_context.sh`) writes a fresh `context_YYYY-MM-DD_HH-MM.txt` every 5 minutes and updates `context_latest.zip`. That snapshot is the single source of truth because it reflects real filesystem state (recently changed files, active task, memory facts, services) — not my possibly-stale memory.

Elijah 2026-04-22 when setting this up: *"save this and present this as where we were when I ask... this is what I wanna see."* Combined with the retired brainstorm, the intent is now "show me the latest snapshot" rather than "reopen the brainstorm."

**How to apply:**
1. Run `ls -t ~/Desktop/AI_CONTEXT/context_*.txt | head -1` to find the newest snapshot. Read that file.
2. Present the contents to Elijah. Lead with the timestamp so staleness is visible ("snapshot is N minutes old").
3. If the newest snapshot is older than 15 minutes, flag it — cron may have stopped. Check `systemctl --user status cron` or the task manager before assuming content is current.
4. For deeper context (arc, archived concepts, cut-but-valuable ideas), also offer `~/Desktop/master_ai_project_notes.md` — that's the brainstorm-residue file from the 2026-04-22 trim.
5. Supplement with MEMORY.md's "Current state" section (session-end memory, master_ai_state, open-flag memories) for anything the snapshot doesn't capture — snapshots are filesystem-oriented and don't carry feedback rules or direction shifts.
6. Do NOT re-present from memory alone. Memory drifts; the snapshot is ground truth.

**Living-doc rules (updated for the snapshot mechanism):**
- Snapshots regenerate automatically — never manually edit one. If content is wrong, fix the source (howwework.txt, memory facts, task list) and the next snapshot picks it up.
- `context_latest.zip` is the Claude-Code handoff artifact — if Elijah starts a new Claude Code session, he pastes the .txt from inside that zip and says "pick up where we left off" (per `~/Desktop/AI_CONTEXT/README.txt`).
- Don't create a new brainstorm-style doc on a whim. If Elijah explicitly asks for one, write it to `~/Desktop/` with a clear name and link it from MEMORY.md.

**Related memory:**
- `project_master_ai_state.md` — detailed captain's-log format for day-level recaps (still valid).
- `project_session_end_2026_04_21.md` — most recent end-of-session recap as of writing.
- `feedback_numbered_5wh_before_execution.md` — when offering updates to the snapshot pipeline itself (e.g. tweaking save_context.sh), use numbered options.
