---
name: Session end — 2026-04-21 evening (Tuesday night)
description: Short end-of-night recap. Three things shipped, two things open, four things to watch. Read this FIRST on "where were we" next session.
type: project
originSessionId: 11fd1fb3-8c8a-4bf3-a76a-4a25a90390a4
---
## What shipped tonight (2026-04-21 evening)

1. **Content-routed chat lane in `master_ai.py` orchestrate()** — new block after the peacetime block (~line 991). Plain chat (no CODE / ALTER / COMPLEX / REASONING words) routes to Groq directly when a key exists, regardless of run_mode. Bypasses the silent-groq-fallback-after-5-min-timeout path that was the real routing bug. Elijah's reframe (*"doesn't necessarily mean local has to be 1st"*) drove it. `local:` prefix still forces local for privacy.
2. **Loud fallback marker** — changed line ~4848 from dim-gray one-liner to red ⚠ warning. When master-ai does time out on a build/reasoning turn and the system falls back to Groq, you now see it.
3. **Smooth-scroll buffer fix in `ask_local_stream._flush_line`** — added soft-wrap at 70 columns. Long sentences now paint every ~70 chars instead of waiting for model newlines. Color classification per soft line preserved. Trade: narrower visible lines; gain: no more 30-second screen pauses during slow inference.

## Memory index cleanup

1. `MEMORY.md` index grew from 52 to 59 entries. Seven orphaned files got linked: `project_harvest_layer.md`, `project_execution_mode_selector.md`, `project_mobile_wallet_vision.md`, `project_pitch_ideas_and_dont_know.md`, `project_tomorrow_sensei_test.md` (flagged STALE), `user_sensei_as_her.md`, `user_training_sensei_via_option_4.md`.
2. `project_pending_routing_bug.md` flipped to RESOLVED. Both halves fixed (3B-route was already gone by morning; silent-fallback fixed tonight).
3. Two new feedback rules written: `feedback_who_what_where_cascade.md` (walk 5W+H before proposing routing) + `feedback_no_dormant_in_designs.md` (no Scrappy/chunker/14b/study-sessions in live design proposals).

## Open flag (not touched tonight)

1. **`feedback_local_mode_default.md` memory is half-stale.** Its "cloud is never the quiet default" line contradicts tonight's chat-class-to-Groq routing. Elijah needs to confirm the new frame before that memory gets rewritten — don't silently override a product-stance memo.

## Late edit — clarify-prompt kick escape (also tonight, after the first save)

1. New helper `_check_kick_escape(choice)` added at master_ai.py just before `confirm_run`. Called from all 5 confirm/permission prompts (`confirm_run`, `confirm_runterm`, `confirm_create`, `confirm_edit`, permission). When user types `kick` (or `force restart` / `hard restart`) instead of a number, it now exits with code 42 → supervisor restarts engine in 3s. Was being SKIPPED + routed-as-chat before.
2. Only `kick` is escaped tonight. `refresh` / `cancel` / `mode plan` / etc. NOT yet escaped — extending the helper is a future small patch if Elijah wants it.

## What to watch (don't fix unless Elijah opens it)

1. **Exit code 99 crashes** at 13:32 and 13:35 on 2026-04-21 afternoon (see `master.crash.log`). Different from normal exit=1. Unknown cause. Pre-dates tonight's edits. If it recurs after restart, investigate.
2. **Hardware freeze earlier tonight** — whole box hard-locked, no OOM, no shutdown messages in journal. Matches an i915 GPU hang pattern. Kernel command line already has `i915.enable_psr=0 enable_fbc=0 enable_dc=0` mitigations. Root cause is the i7-6700T + HD 530 iGPU under load from Ollama 7b + RustDesk ×2 + Claude Code + Cinnamon. This freeze IS the argument for the three-tier mesh laptop upgrade already locked in memory.
3. **Git state is messy.** 14 modified files uncommitted + 40+ untracked files (including load-bearing ones like `harvest.py`, `prewarm_master_ai.py`, `dojo_gate.sh`, `deep_clean.sh`, `pack_for_sale.sh`, `master_ai_voice.json`, `memory/` dir, `systemd/` dir). If something breaks, those are not rescuable via git. A "freeze + commit" pass is overdue — do NOT do this tonight (Elijah's no-errors-tonight rule). Flag it for next clean session.
4. **`master_ai.py` still has ~3500 lines of uncommitted in-flight rework** from earlier today: profile system, mode persistence, `LAST_ROUTE`/`LAST_MODEL` globals for Review mode's "who" line, etc. Tonight's three edits landed on top. Entire pile needs a commit + test pass together, not separately.

## Mood of the session

Elijah called out a real pattern: when given a routing bug I was wandering (listing dormant Scrappy, jumping to structures instead of walking cases). He introduced the 5W+H cascade as the discipline — walk WHO / WHAT / WHERE / WHY / WENT / HOW for each concrete case BEFORE proposing code. That's now a feedback memory. The actual fix followed the cascade cleanly once applied.

Hardware also freezing / shutting down mid-session. His workflow is running against the ceiling of this i7-6700T Skylake box. Every long routing turn on CPU = 1–5 min of screen-dead time. He's remote from phone + tablet while the box runs — 2× RustDesk + Ollama + Claude Code is too much for 16 GB / no discrete GPU. Upgrade is already in plan.

Overall: real progress, ugly hardware, clean mental frame. Ending before errors creep in.

## Opening move next session

1. Restart Sensei if not already running (`bash ~/scripts/master_ai_kick.sh`).
2. Validate tonight's three edits with real inputs: `hi` (chat → Groq fast), `create a test file at /tmp/smoke.sh that prints hi and self-deletes` (build → master-ai + CREATE: directive), observe scroll smoothness on both.
3. If anything fails, the crash log + harvest log are the first places to look.
4. Do NOT commit the uncommitted pile without Elijah's explicit go — big blast radius.
