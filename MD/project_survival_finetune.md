---
name: Scrappy — specialist model hook inside Master AI
description: Master AI's orchestrator routes survival/DIY/off-grid keyword questions to any `scrappy`-named Ollama model when present. The MODEL itself, its dataset, its kit, its stage ladder, and its fine-tune work are a SEPARATE PROJECT at ~/off_grid_kit/. This file only documents what Master AI does when a Scrappy model is pulled onto the box.
type: project
originSessionId: 6dd5863d-4ed5-45b6-ba4a-581fbc1b9fc0
---
## What lives in Master AI (scope of this file)

- `master_ai.py` has `SURVIVAL_WORDS` + `_scrappy_model_present()` + orchestrator route #6b.
- When any Ollama model tag contains "scrappy" AND the user's question matches a survival/DIY/off-grid keyword, Sensei routes there instead of the generic 7b/14b. Works in Local Mode and Connected Mode both.
- No-op when no scrappy model is pulled.

## What does NOT live in Master AI

The following moved to the SEPARATE project at `~/off_grid_kit/` on 2026-04-19 — they are not Master AI features:

- Kit physical bundle (fire steel, tarp, knife, water filter, solar charger).
- Stage 0 pocket guide and mesh join card (physical card artifacts — now at `~/off_grid_kit/stage0_pocket_guide.md` and `mesh_join_card.md`).
- 6-stage civilization ladder (Camp → Hut → Village → Town → City → State → Country).
- Scrappy fine-tune dataset curation, training, and publishing pipeline.
- Editions / pricing tiers tied to the kit.

Do not re-pull those into Master AI's PROJECTS.md or memory unless Elijah explicitly asks.

## Boundary rule

- Master AI DETECTS whether Scrappy is on the box. It does not build, ship, or own Scrappy.
- If the off-grid project ever ships a model named `scrappy:*`, Master AI picks it up automatically via the orchestrator hook. No Master AI code change needed.
- Conversely, Master AI does NOT depend on Scrappy — everything still works with just the trifecta.
