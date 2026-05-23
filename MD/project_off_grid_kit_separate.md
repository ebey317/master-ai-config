---
name: Off-Grid Kit — separate project (not Master AI)
description: The off-grid kit, Scrappy fine-tune, 6-stage civilization ladder, and physical bundle live at ~/off_grid_kit/ as their own project. Split out from Master AI on 2026-04-19 at Elijah's direction. Master AI only has the orchestrator hook that routes to a `scrappy`-named model if one is present.
type: project
originSessionId: 6dd5863d-4ed5-45b6-ba4a-581fbc1b9fc0
---
## Project root
`~/off_grid_kit/`

## What lives here
- Stage 0 pocket guide (`stage0_pocket_guide.md`) — laminated no-power card content.
- Mesh join card (`mesh_join_card.md`) — one-page how-to for federating with another operator.
- **BioVega — Windmill Electric System Field Manual** (`biovega_field_manual.md`) — added 2026-04-21. 18-page step-by-step build guide for a DIY wind-electric system from scavenged parts (treadmill motor / e-bike / mobility chair / marine DC / PM alternator → charge controller → battery → fuse box → load). "Read one page, do only what that page says" pacing. Ends with Elijah's line: *"BioVega is not the shelter. BioVega is what allows the shelter to exist. Generation is rebuildable. Storage is sacred. Control protects memory."* BioVega = the power-system component of the kit (distinct from the shelter it powers).
- Future: Scrappy fine-tune dataset curation, training pipeline, stage curriculum.
- Future: physical bundle suppliers (fire steel, tarp, knife, water filter, solar charger).

## Why separate
Elijah's call (2026-04-19): *"These are different projects."* and *"separate project."* The off-grid kit is a standalone offering with its own buyer story. Master AI is the host platform; the kit is a product that uses the platform.

## Interface with Master AI
- If the kit ever ships an Ollama model named `scrappy:*` and that model is pulled on a Master AI box, Master AI's orchestrator automatically routes survival-keyword questions to it. No code change needed on either side.
- Master AI's mesh feature is what the kit's "mesh join card" explains to buyers.

## Rule
Do not fold kit work back into Master AI unless Elijah explicitly asks. Master AI's PROJECTS.md entry for "Off-Grid Civilization Information" has been trimmed to just the Master-AI-side responsibilities: orchestrator hook + scanner UIs. Everything else is this project's domain.
