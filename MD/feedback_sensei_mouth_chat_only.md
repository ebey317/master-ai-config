---
name: Sensei input/output split — text box in, pop-up out
description: Sensei has two surfaces — a stationary input text box where the user types, and a pop-up that holds ALL of Sensei's output (chat replies, questions, visual demos like matrix rain). The pop-up is Sensei's mouth. Separation makes it run smoother per Elijah 2026-04-25.
type: feedback
originSessionId: 9ea3cc6d-3fe6-40db-978e-7830d6331e59
---
Two surfaces, clean split:

1. **Input text box (user types here)** — stays in its normal stationary place. User input lane only. (TBD what else lives in this window.)
2. **Pop-up = Sensei's mouth** — receives ALL of her output: chat replies, questions back to the user, AND visual content (matrix rain, demos, animations). One unified output surface. Opens when needed, can stay open across exchanges, closes when done.

Sensei's "mouth" is the pop-up, NOT the inline chat box. The inline box is for the user's keystrokes.

**Why:** Elijah's call 2026-04-25 — "separating them will make it run smoother." Mixing his input keystrokes with her output replies on the same surface conflates two roles and pollutes both. One surface per direction is cleaner.

**How to apply:**
- Sensei text replies render in the pop-up, not inline next to the input prompt.
- Sensei questions to the user (e.g. plan-mode clarifiers) render in the pop-up.
- Visual/interactive content (matrix rain, demos) renders in the pop-up via `x-terminal-emulator` / `gnome-terminal` launcher, same surface.
- User keystrokes / scratchpad-style extended input live in the inline text box, not the pop-up.
- Scripts that detect missing TTY (running under Sensei's pipe) should re-launch in the pop-up rather than spew bytes inline.
- Static-ASCII chat fallbacks for animated content are wrong. Pop-up is the only home for animation / visual output.
