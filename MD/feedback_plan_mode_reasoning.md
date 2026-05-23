---
name: Plan Mode Reasoning Style Validated
description: Sensei's new Plan mode (reasoning + clarifying questions + numbered plan + <PLAN READY> marker, landed 2026-04-20 evening) produces output Elijah recognizes as Claude-like — keep the pattern.
type: feedback
originSessionId: 01013ad7-2ca6-4c2c-a21f-0331a77353a0
---
Plan mode's conversational-reasoning behavior is validated. It reasons out loud, asks a clarifying question when needed, then commits to a numbered plan by emitting `<PLAN READY>` as the last line. The 1/2/3/4 prompt appears only when the marker fires.

**Why:** 2026-04-20 evening, right after the core loop landed, Elijah tested Plan mode end-to-end and said: *"You can write my projects in though to him and I like how he takes notes. He's responding kind of like you."* That's a voluntary positive — he's saying Sensei's Plan-mode output feels like Claude's responses. Specifically: produces structured notes, reasons before committing, asks the right clarifying question, numbered steps instead of shell commands.

**How to apply:**
- Keep the Plan-mode prompt shape in `master_ai.py` (the one that instructs the model to reason, ask ONE clarifying question if needed, then commit with `<PLAN READY>`). Don't simplify it into single-shot plan generation.
- The `<PLAN READY>` marker detection (regex in the plan branch) is the mechanism that gates the 1/2/3/4 prompt. Don't replace with heuristics (e.g., "any numbered list triggers ready") — the marker lets the model control pacing.
- The directive stripper (RUN: / CREATE: / EDIT: → `(step would)`) is load-bearing. Plan mode emits PROSE; if directives leak they get softened, not ignored.
- If future work adjusts Plan mode, preserve these three properties: (1) reasoning happens visibly before the plan, (2) model controls plan-ready pacing via a marker, (3) no shell directives in plan output.
- Elijah chose the name "Plan" (not "Brainstorm" or "Draft") partly because Claude Code's plan mode already set his expectation. Plan mode in Sensei IS meant to feel like Claude Code's plan mode.
