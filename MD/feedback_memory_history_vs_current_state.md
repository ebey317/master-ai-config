---
name: Historical session-log HEADs are intentional — leave them alone
description: Don't propose to "fix" stale HEAD references in historical session-log entries; only current-state hooks should track latest HEAD. And re-read live MEMORY.md before offering memory updates.
type: feedback
originSessionId: 026ab61e-8cba-4268-801a-309c4199d383
---
Two related rules.

**Rule 1 — Only current-state hooks track HEAD. Historical session-log entries keep their original HEAD on purpose.**

The `Sensei Current State` line and `project_master_ai_state.md` description/status are CURRENT-state surfaces — those should always point at the live HEAD. But `Session end — 2026-04-21 evening` and similar dated entries are HISTORY — their HEADs and statuses are snapshots of that moment and must stay that way so the timeline reads truthfully.

**Why:** Elijah corrected me 2026-04-25 evening when I offered to "fix" `2a51452` references in MEMORY.md after HEAD had moved to `965adc9`. He'd already updated the current-state hook; the remaining `2a51452` mentions were intentional history.

**How to apply:** Before offering a memory update, ask which surface I think is stale. If it's a dated session-log entry, leave it — it's a fixed record. If it's a current-state index hook or `project_master_ai_state.md`, then update it.

**Rule 2 — Re-read live MEMORY.md before proposing memory updates.**

My in-context view of MEMORY.md may be cached from earlier in the session. Elijah edits memory directly (or via `~/scripts/sync_hard_limits.py`), so what I "saw" may be hours stale.

**Why:** Same incident — I told Elijah his MEMORY.md hook still said HEAD = `2a51452` when in reality he'd already updated it to `965adc9`. The cached view embarrassed me into offering a fix that wasn't needed.

**How to apply:** Before saying "memory shows X" or "want me to update Y," re-read the file (Read tool, not from in-context cache). If the user says "already fixed," trust it without verifying — they're closer to the live state than my context is.
