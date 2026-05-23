---
name: "Go through everything since download" means project-wide audit, not one bug
description: When Elijah asks to "go through all the files / pipe everything together / run the sequence / go through everything since download" he means a PROJECT-WIDE end-to-end audit — not a multi-surface fix for one bug. Missed this three days before 2026-04-23; he flagged the miss.
type: feedback
originSessionId: e425f1a0-6553-45fa-8635-026bab0850c6
---
When Elijah asks to "go through all the files," "pipe everything together," "run the sequence," or "go through everything since download," he means a **project-wide audit**, not a multi-surface fix to one current bug.

**Why:** Flagged 2026-04-23 evening. He had asked some days earlier for a pipe-through sequence check and I only did it at the scope of a single active bug. He confirmed the ask was much larger — every file, every feature, every config, since the install/download point. The URL-invention fix I did today is a SINGLE-BUG version of the pattern; it is NOT the template for what he meant.

**How to apply:**
- When the phrase lands, don't auto-scope to the current bug. Ask what the starting marker is ("since download" = since ~/scripts git init? since first Master AI commit? since Ubuntu install?), then propose a scope menu (orphan hunt / feature trace / system-prompt coherence / full sweep) before firing.
- The full-sweep output should be a structured report at `~/Desktop/master_ai_audit_<date>.md` — not inline chat — so he can scan offline on phone.
- Distinguish clearly in the report: what is LIVE, what is STAGED (disk but not running), what is ORPHANED (on disk, never wired), what is STALE (references a feature that was removed/archived).
