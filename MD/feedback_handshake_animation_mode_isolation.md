---
name: One Thought Channel At A Time (Isolation)
description: Only ONE thought stream surfaces at a time — thinking locks out idle tips, idle locks out thinking, handshake locks out both. Never concurrent thought sessions. (Animation of the handshake itself was vetoed — see feedback_no_polish_creep.md.)
type: feedback
originSessionId: c53f933b-955f-4b1d-8b2b-dc75f15b261b
---
**The rule (2026-04-21):**

**One thought channel at a time — driven by activity state, not mode.**

Elijah's exact words 2026-04-21: *"when it's thinking it doesn't need to be doing idle thoughts; when it's idle it doesn't need to be thinking; when it goes to handshake it doesn't need to be doing idle or thinking — it just needs to be [showing the handshake thought]. Never more than one or two. Never more than one thought session."*

The three thought channels are mutually exclusive:
- **Thinking** (model is processing) → thinking animation only. Suppress idle tips.
- **Idle** (waiting on user input) → idle tip rotation only. No thinking animation.
- **Handshake** (mode transition) → handshake thought only. No idle, no thinking.

**Why:**
Overlapping thought streams confuse the state signal. If the spinner says "thinking…" while an idle tip also prints, the user can't tell what the app is actually doing. Keeping one channel live at a time keeps the signal clean.

**What's already correct in code:**
`sensei_tui.py:_render_tip()` (line ~494) already has a clean three-state exclusive switch: typing → empty, thinking → thinking line, else → idle tip. Thinking and idle cannot appear in the tip window at the same time. `local_thinking_start()` in master_ai.py delegates to the TUI's `start_thinking()`, so no parallel spinners either.

**What's still technically overlapping (but NOT to be "fixed" with polish):**
The Plan→Review handshake (`master_ai.py:~5294`) prints a one-line `"▶ handoff: Plan → Review — executing plan..."` and then `handle()` immediately fires, starting the thinking anim. The handoff print + thinking overlap briefly. Elijah initially asked for a dedicated handshake thought/animation to solve this, but THEN vetoed it 2026-04-21: *"adding these little elements take away from my ground process, speed and power."*

**So: do NOT build a handshake animation or a dedicated handshake thought channel.** The brief overlap is acceptable cost — the color flip already carries the signal, and layering more ornament on top hurts speed + power. See `feedback_no_polish_creep.md`.

**How to apply:**

- Keep the existing thinking ↔ idle mutex. Don't regress it.
- When adding any new "thought emitter" (status blurbs, drift reminders, recall flashes), pick one of the existing channels — thinking or idle — and make it mutually exclusive with the other. No multiplexing, no new channels.
- Don't introduce a "handshake thought" channel unless Elijah explicitly re-opens that door.
- Related: `feedback_no_polish_creep.md` (why we didn't animate the handshake), `feedback_handoff_color_flip_validated.md` (the color flip IS the handshake signal), `feedback_mode_stoplight_colors.md` (color mapping), `project_idle_gate_rules.md` (idle timer doesn't count while thinking — same principle).
