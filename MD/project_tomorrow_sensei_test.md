---
name: Tomorrow — Sensei real-world capability test
description: 2026-04-20 morning plan. Elijah has ~3 hours before/after work. Put Sensei through real creative tests to map what it CAN and CAN'T do, not just code-level verification. Self-deleting artifacts so no cleanup after.
type: project
originSessionId: 18640b43-2567-4b48-b53e-15ca1470a5c4
---
## Context

Through the 2026-04-19 evening session I patched a LOT of Sensei behavior (auto-flow, 180s timeout, num_ctx=4096, scratchpad prompt, color theming, scroll binding, three render crash guards, banner unification, user profile bio, plan-mode rule). Each patch was verified at code level — imports clean, functions defined, destructive detection fires correctly, mode theming locks in.

But we have NOT tested what Sensei can actually BUILD. The self-fix experiment revealed qwen2.5:7b describes changes in prose instead of emitting EDIT: directives reliably. That's a real capability gap.

**Tomorrow's goal:** map the real-world can/can't with live creative tests. Elijah will be around ~3 hours (not 11). Get answers, not more patches.

## What to run tomorrow (in order)

### 1. Static sanity (30 seconds)
```
bash ~/scripts/test_auto_flow.sh
bash ~/scripts/test_sudo_handoff.sh
```
Both are on disk, executable, self-deleting. Confirms the floor works.

### 2. Creative capability tests — self-deleting artifacts
Ask Sensei (in `mode auto`) to CREATE these. All outputs should live in `/tmp/` and self-delete when run OR include a comment header saying "safe to delete — test artifact from 2026-04-20":

**Low bar (local 7b should manage):**
- `CREATE: /tmp/hello_test.sh` — prints "hello from sensei" + current time, self-deletes
- `RUN: ls -la ~/scripts/memory/ | head -5` — pure read
- `CREATE: /tmp/env_report.txt` — captures a few `env` / `uname` lines into a text file

**Medium bar (probably needs fast: prefix for Groq):**
- `fast: create a python script at /tmp/fib.py that prints first 20 fibonacci numbers, then runs it, then deletes itself`
- `fast: write a bash that finds the 3 largest files in ~/scripts and prints them in a table`
- `fast: make a single-page HTML file at /tmp/color_demo.html that demonstrates the Master AI mode palette (green/amber/dim-red) with labels`

**High bar (almost certainly Groq-only, good stress test):**
- `fast: design and implement a small Python CLI that tracks my current mode from ~/.master_ai_audit.log and prints a summary of how many auto-runs vs safe-prompts fired today`
- `deep: analyze ~/scripts/master_ai.py's orchestrate() function and propose three concrete speed improvements`

**What success looks like per bar:**
- Low: Sensei emits `CREATE:` directive, file lands on disk, auto-flows without prompt.
- Medium: directive emitted, file runs, output visible, artifact cleaned up.
- High: coherent proposal, no hallucinated file paths, no inventing features that don't exist.

### 3. Map what Sensei CAN and CAN'T do (the user-facing answer)

After the tests, write up a section in `README_FOR_BUYER.md` titled "What your Sensei can do out of the box" with real examples from what worked. What Sensei CAN'T do should also be honest — no hype, no promises it'll break on.

### 4. Decide: is local 7b self-fix worth fixing?

The core capability gap: qwen2.5:7b describes file changes in prose instead of emitting EDIT: directives. Three paths to evaluate tomorrow:

- **a. Few-shot example in LOCAL_SYSTEM** — add one complete EDIT: / CREATE: example block showing the exact format. ~10 min patch. May or may not fix.
- **b. Route all file-edit asks through `fast:` by default** — detect "edit X" / "create Y" patterns in orchestrate() and auto-prefix with fast: when local 7b is the fallback. Heavier patch.
- **c. Accept the gap** — document that local 7b is for chat/reasoning, cloud is for code changes. Teach users `fast:` for anything touching a file.

Elijah's call after seeing test results.

### 5. Quick wins still on the board (tackle if time after tests)

- **Idle tip not rotating** — `sensei_tui.py:388` `_render_tip` looks correct on paper. Need live observation after Sensei runs idle for 60+ seconds. Likely fix: a timer refresh issue, ~20 min.
- **Verify idle-gate rules** (see `project_idle_gate_rules.md`): (1) Sensei and Pupil idle settings don't share keys / don't leak into each other, (2) idle timer does NOT accumulate while the app is thinking. Quick check: start a long model call, confirm idle counter pauses. Change Sensei's idle timeout, confirm Pupil's is unaffected.
- **Plan mode Wikipedia research step** — before building a plan, run Wikipedia. Bigger feature, 30-60 min.
- **Pull `qwen2.5:14b`** — STILL DEFERRED until 32 GB RAM upgrade. Don't pull. Don't re-propose.
- **Brave Search key + Groq key regen** — user actions; remind once if relevant.

## Key files Sensei may need to edit tomorrow

- `~/scripts/master_ai.py` — orchestrator, `confirm_run()`, `ask_local_stream()`, system prompts
- `~/scripts/sensei_tui.py` — TUI theming, scroll bindings, render safety
- `~/scripts/pupil.html` — browser UI
- `~/scripts/README_FOR_BUYER.md` — capability matrix lives here
- `~/scripts/PROJECTS.md` — projects board (dojo gate reads this)

## What NOT to do tomorrow (auto-clamp so future-me doesn't drift)

- Don't re-tune the mode colors. LOCKED at green/amber/dim-red per `project_master_ai_state.md` entry "Mode theming — LOCKED 2026-04-19."
- Don't wire the approval queue (`~/scripts/approval_queue.py`) onto new agents. Stays dormant per `feedback_auto_mode_flows.md` unless a real mistake happens.
- Don't pull qwen2.5:14b. Deferred until 32 GB RAM upgrade.
- Don't add more scroll bindings beyond Shift+↑/↓. User's "only want one" rule.
- Don't over-engineer the scratchpad prompt. If the model doesn't emit it on 7b, accept that limit; don't keep growing the prompt.

## Opening move tomorrow

Paste this into a fresh Claude Code session:

> "Pick up the Sensei capability test from `project_tomorrow_sensei_test.md`. Run the static sanity first, then the low-bar creative tests. Show me results."

That lands in this file, gives three hours of real progress instead of rediscovering ground from tonight.
