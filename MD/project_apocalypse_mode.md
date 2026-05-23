---
name: Apocalypse Mode — ABANDONED 2026-04-20
description: Two-mode product concept scrapped. Master AI is single-mode "local-first with cloud opt-in." Do not reintroduce Apocalypse Mode, chunker surfacing, or peacetime/apocalypse framing without explicit permission.
type: project
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---

## STATUS: ABANDONED (Elijah, 2026-04-20 early morning)

> "abandon apocalypse mode and prioritize these projects one at a time"

**Why:** Chunker was archived 2026-04-19 (no home in the UI — violated give-every-feature-a-home). Apocalypse Mode was the framing that justified the chunker. With chunker gone, the two-mode split added complexity without product value. Elijah wants to finish one thing at a time instead of maintaining a second mode.

**How to apply:**
- Master AI is now **single-mode**: local-first orchestrator, cloud is opt-in per-message (`fast:`/`deep:`) or per-session (`mode connected`). See `feedback_local_mode_default.md`.
- Do NOT reintroduce the words "Apocalypse Mode" / "Peacetime Mode" in UI, docs, installer, or code paths.
- Do NOT resurface the chunker. It stays archived.
- The off-grid / severance work still matters, but it lives in the **separate off-grid kit project** (`project_off_grid_kit_separate.md`), not inside Master AI as a mode toggle.
- If Elijah ever goes severed, he pulls a bigger local model by hand — no product-level "apocalypse prep" flow needed.

## What survives from the old framing

- Local-first default (already in `feedback_local_mode_default.md`).
- `llava` stays on disk for scrap scanner + apothecary — those are features, not a mode.
- Connectivity-aware routing (fall back to local when cloud unreachable) is fine as silent router behavior, NOT a user-facing mode.
- The "your AI doesn't die when the cloud does" marketing line can still be true without a named mode — it's just a property of the product.

## What's dead

- Two-mode toggle in `master.sh`
- `apocalypse_prep.sh` pre-download helper (never built, don't build)
- Peacetime/Apocalypse labels in UI or docs
- Chunker as an Apocalypse-Mode command
