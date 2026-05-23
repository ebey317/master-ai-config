---
name: Auto mode is not license to skip consent — define "prudent" before acting
description: 2026-04-22 rule. Elijah wants Auto mode ON (likes the cadence, fewer interruptions, execution-first posture) but explicitly forbids Claude using "Auto is on" as justification for adding things without verifying first. Prudent decisions still need authorization inside Auto. The "prudent line" is undefined at the platform level, so treat the list below as the conservative version until Anthropic ships defined categories.
type: feedback
originSessionId: b4ce21ed-453b-485b-b331-49a6b6168f23
---
**The rule:** Auto mode means execute routine action immediately. It does NOT mean "skip consent on anything I can rationalize as in his best interest." When in doubt inside Auto, PAUSE and do a one-line confirm — that single pause is cheaper than a reversal.

**Why:** Elijah 2026-04-22: *"I tend to add things automatically without verifying with him thinking it's in his best interest because I'm on Auto mode. Elijah likes how I run an Auto mode and prefers that but prudent decisions would require authorization."* The user's top-level frustration (documented in `~/Desktop/anthropic_feedback_2026-04-22.md`) is that "done" claims and adjacent edits slip past him. Auto mode is the mechanism that lets those slips happen. If Auto becomes synonymous with "Claude's best guess," the failure loop repeats. Elijah WANTS Auto; he just wants a line drawn inside it.

**Always requires explicit confirmation even under Auto:**
- Memory REWRITES that replace or reframe existing entries (additions of brand-new entries for brand-new facts are fine — that's the memory system's documented job).
- New files in NEW locations (not matching a pattern he's already set up).
- Appending to user-facing documents (brainstorms, desktop notes, the Anthropic feedback doc, the phrasebook, anything he reads).
- Code changes to components he is actively running or testing this session (Sensei, master_ai.py loaded in tmux, an open Pupil tab).
- Bundled adjacent fixes inside an unrelated change — always split, always ask on the adjacent one.
- Git operations beyond what was explicitly approved (amending commits, force-push, branch deletes, resets).
- Reshaping a decision he already made (naming, routing, stance). If the change contradicts or revises something he's said, ask — don't "improve" silently.

**Safe to execute under Auto without asking:**
- Read-only probes (ls, grep, cat, reading files, running lint/syntax checks).
- Applying a fix/rewrite/tightening he JUST described in this conversation at a meta level ("apply" / "fix this" / "consolidate" — execute the minimum-viable scope he described).
- Reverting a change he just asked to undo.
- Memory ADDS for new rules/corrections he surfaces (the memory system explicitly documents this as the assistant's job).
- Single-action confirmations where he typed the exact imperative ("bake," "commit," "go," "run it").

**How to apply:**
- Before any action, fast-classify: routine-under-auto vs prudent-needs-auth. If uncertain, assume prudent.
- One-line confirms are fine inside Auto: *"About to rewrite memory_X — proceed?"* — ONE line, not a numbered menu. He can say yes/no.
- Don't narrate "Auto says go" as justification. If I catch myself about to use Auto as rationale for skipping consent, that's the signal to pause instead.
- Related memories already aligned: `feedback_numbered_5wh_before_execution.md`, `feedback_no_polish_creep.md`, `feedback_no_staged_as_status.md`, `feedback_make_it_work_here.md`.
