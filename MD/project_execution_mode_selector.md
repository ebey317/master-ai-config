---
name: Execution-Mode Selector (Local / Cloud / Mixed)
description: Elijah's 2026-04-21 idea — replace the per-message `fast:` prefix with a session-level selector: Local / All-Cloud / Mixed. Saw "MODEL:AUTO+CLOUD" in status line and realized that's already the mixed path. Queue for tomorrow.
type: project
originSessionId: 01013ad7-2ca6-4c2c-a21f-0331a77353a0
---
**The idea:** typing `fast:` in front of every prompt is too much friction. Offer a **selector** so the user picks once per session how much cloud they want:

- **Local** — run everything on local models only. Privacy default. (Already exists via `local:` prefix or when cloud keys are absent.)
- **All Cloud** — every prompt routes cloud-first (Groq/DeepSeek/Gemini). Speed default. Equivalent of `mode connected` today, which is already a feature — need to surface it more.
- **Mixed** — smart routing. Local by default, cloud on demand. This is what Elijah already saw as `MODEL:AUTO+CLOUD` in the status line when cloud keys are present. The name just isn't great.

**His exact words 2026-04-20 night:** *"I think that might be a bit much if I want him to run fast to either have a choice of either doing it local / all cloud / mixed and I seen it say local plus cloud up there save it for tomorrow."*

**What needs design (tomorrow):**
- UI surfacing — probably a status-line badge that shows `LOCAL` / `CLOUD` / `MIXED`, and a single command like `mode local` / `mode cloud` / `mode mixed` to switch. Already partially exists: `mode connected` = cloud-first. Need the full triad.
- Relationship to Plan/Review/Auto — those are SAFETY modes (who-confirms-what). Local/Cloud/Mixed is a COMPUTE mode (where-does-thinking-happen). Orthogonal. Both should coexist in the status line: `MODE:PLAN  │  COMPUTE:MIXED  │  ...`
- Default: Mixed (current behavior when keys are present) — but make it explicit/visible rather than hidden under the `AUTO+CLOUD` model label.
- Renaming: `MODEL:AUTO+CLOUD` → `MODEL:AUTO  │  COMPUTE:MIXED` (or similar). The model label and the compute-mode label are different concerns.

**How to apply tomorrow:**
- Land this AFTER going through the project list + gate-testing tonight's work. Don't build on top of an untested core loop.
- Small scope — mostly label/status-bar changes + a couple of new keyword handlers. No new models, no new routing logic.
- Keep the `fast:` / `deep:` / `local:` per-message prefixes — they stay as overrides. The selector just sets the default.
- Honor "Local Mode is Default" feedback memory — default out of the box must stay Local, even if keys are present. Mixed requires explicit opt-in OR the user has already set up keys (softer signal of consent).
