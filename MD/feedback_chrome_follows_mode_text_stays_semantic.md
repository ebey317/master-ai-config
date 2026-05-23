---
name: Chrome Follows Mode, Text Stays Semantic
description: Sensei's CHROME (frame borders, header, status, legend, tip) changes color with the mode (Plan=red, Review=amber, Auto=green). TEXT content stays on stable semantic colors (blue=file/info, yellow=plan steps, green=voice, orange=caution). Never flip text color with mode.
type: feedback
originSessionId: c53f933b-955f-4b1d-8b2b-dc75f15b261b
---
**The rule (2026-04-21):**

Sensei has TWO color systems, and they are NOT the same:

1. **Chrome** (frame borders, header bar, status line, legend, tip row) — follows the MODE accent. Plan = deep red `#cc0000`, Review = amber `#c7761a`, Auto = green `#1a7a3a`. These repaint when `set_mode()` fires. Good — this is how Elijah sees what mode he's in at a glance.

2. **Chat content text** — stays on stable SEMANTIC colors regardless of mode. These are set by `master_ai.py:_paint_line()`:
   - Blue (BC) — scratchpad, info quotes, file paths
   - Bold green (BG) — AI conversational voice (default)
   - Bold yellow (BY) — PLAN steps, numbered lines, RUN/CREATE/EDIT directives
   - Orange (BO) — warnings, caution, danger
   - Dim blue (DIMB) — source footers, URLs

Elijah's own words 2026-04-21: *"I don't want the text to flip with its color. All of that is already coordinated in the system that I can read. I know to look for red blue and green stuff certain lines. I need that to be one color code that doesn't change for me so I can understand the borders know that that's fine with the text. Needs to stay one color for the chain of events when it's working so I can identify blue file, red and removal."*

**Why:**
His eye is trained on the semantic meaning of each color. Blue means "file or reference." Yellow means "plan step or command about to run." Red-in-text means "danger/removal." If text colors flip with the mode, his trained mental model breaks on every mode switch — he can't tell a file from a delete from a plan step at a glance anymore. Mode should be communicated by CHROME only.

**How to apply:**

- Any new output surface that shows chat content must use `noinherit` so the Frame's mode-accent doesn't bleed. Current fix: `sensei_tui.py` line ~265 `self._output_window = Window(... style="class:chat" ...)` + `_build_style` defines `"chat": "noinherit"`.
- Chrome surfaces (status, legend, tip, frame, header) SHOULD follow `{accent}` — do not change that.
- If a future refactor wraps chat in another container, preserve the noinherit property.
- The `_paint_line` function in `master_ai.py` is the canonical semantic color authority. Do not change its mappings without Elijah's explicit ask — his training is on those exact colors.
- Prior slip (2026-04-21): I misread "text have been going with the modes" as approval of the behavior, reverted the noinherit fix. Elijah corrected within an hour — re-applied. Don't re-invert.

**Related:**
- `feedback_mode_stoplight_colors.md` — the mode-accent mapping (chrome colors)
- `feedback_light_terminal.md` — light background means dim text doesn't work
- Phone-remote readability of red chrome is a separate problem with a separate fix (use Pupil for phone).
