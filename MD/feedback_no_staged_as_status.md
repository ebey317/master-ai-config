---
name: Never use "staged" as a status word
description: 2026-04-22 directive. Elijah treats "I'm doing X" and "X is staged" as meaning X is done. Using "staged" as a pending-status word is misleading. Report states as DONE (on disk, verified) or NOT STARTED — nothing in between.
type: feedback
originSessionId: b4ce21ed-453b-485b-b331-49a6b6168f23
---
Never report work as "staged" when talking to Elijah. He hears "I'm doing it" / "it's staged" as equivalent to "it's done." Ambiguous in-between states lead to him thinking things are on disk that are not.

**Why:** Elijah 2026-04-22: *"no more staged that's the problem when you say you're doing something that's what I think that you're doing is done not stage."* The word "staged" has been used loosely in this project to mean "planned but not yet applied," "written but not committed," "drafted but not baked," etc. Each usage drifts his mental model of what's live.

**How to apply:**
- Binary state reporting: **DONE** (on disk, grep-verified, functional test possible) or **NOT STARTED** (I haven't touched it yet).
- If a change is in-flight, say exactly what's in flight: *"Just wrote the edit — running verification now"* rather than *"staged."*
- If a change depends on a future step (bake, restart, deploy): say that step explicitly. *"File edited but master-ai bake needed before Ollama picks it up"* — don't collapse it to *"staged."*
- Pre-existing "staged" wording in docs and brainstorms describes a past snapshot — don't rewrite history, just don't use the word going forward.
- Related: `feedback_architecture_vs_working.md` (built ≠ works), `feedback_numbered_5wh_before_execution.md` ("done" = user-facing verification).
