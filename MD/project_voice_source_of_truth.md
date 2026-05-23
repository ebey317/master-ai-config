---
name: Master AI voice — one source of truth
description: All rotating copy (idle tips, trademark quotes, Elijah's verbatim lines, Sensei thinking phrases, Pupil did-you-knows) lives in ~/scripts/master_ai_voice.json. Sensei + Pupil both read it so both UIs speak in one accord.
type: project
originSessionId: 6dd5863d-4ed5-45b6-ba4a-581fbc1b9fc0
---
## Where the voice lives
**`~/scripts/master_ai_voice.json`** — single file, six sections:
- `elijah_verbatim[]` — Elijah's own raw quotes with title/date/text. Do NOT polish.
- `elijah_short[]` — punchy one-line extracts of verbatim quotes, rotate well in idle bubbles.
- `quotes[]` — brand-polished derivatives (safe for slideshow/marketing/idle).
- `tips[]` — Sensei command hints (`{cmd, desc}` pairs).
- `pupil_tips[]` — "did you know" one-liners for Pupil.
- `thinking[]` — ninja-mood rotating phrases while the AI is generating.

## How the UIs consume it
- **Sensei (`master_ai.py`)** — reads directly on startup via `_load_voice()`. `_LOCAL_THINKING_LINES` and `_IDLE_TIPS` are built from the JSON. Empty-cmd tips render as italic quotes; cmd+desc tips render as yellow-cmd / cyan-desc.
- **Pupil (`pupil.html`)** — fetches `/thoughts` from stt_server synchronously at init (same pattern as `/profile`). Builds `IDLE_PHRASES` from `quotes` + `pupil_tips`; replaces `THINKING_PHRASES` with `thinking`.
- **stt_server** — new `/thoughts` endpoint just returns the JSON as-is.

## Why it matters (Elijah's framing, 2026-04-19)
*"I need the thoughts to be uniform for idle and thinking. It is a trademark part of us — those wonderful quotes... those are real words real raw emotion and thoughts. I create the definition."*

Do not hardcode new quote copy in `master_ai.py`, `pupil.html`, `sensei_tui.py`, or any script. **Edit the JSON. Both UIs inherit.** The behavior contract at `~/.sensei_behavior.md` points Sensei to this file as the canonical source.

## When to update it
- Elijah says something framing-worthy in a session → add to `elijah_verbatim[]` with the date, and extract a short form to `elijah_short[]`.
- A brand line crystalizes → add to `quotes[]`.
- A new Sensei command ships → add to `tips[]`.
- A new Pupil feature lands → add to `pupil_tips[]`.
- Never delete `elijah_verbatim[]` entries — they're historical record of his voice.
