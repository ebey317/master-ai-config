---
name: Creator bypass — Elijah never takes the dojo test
description: The dojo gate honors the creator. Even when sealed mode is on, Elijah enters unrestricted — he built Master AI, he's already passed. Mechanism — ~/.master_ai_creator sentinel file.
type: feedback
originSessionId: 6dd5863d-4ed5-45b6-ba4a-581fbc1b9fc0
---
**Rule:** Elijah never has to "turn in a project" to enter his own dojo. The gate always lets him skip, even in sealed mode.

**Why:** His words (2026-04-19): *"I never have to take a test to get in the dojo. I've already passed. Hint — I created Master AI."* Making the creator face the buyer's ritual is wrong. The gate is a user discipline, not a creator ceremony.

**How to apply:**
- Mechanism lives in `~/scripts/dojo_gate.sh` — three functions: `is_sealed()`, `is_creator()`, `is_sealed_for_user()`. The blocking checks throughout the gate all use `is_sealed_for_user`, which is `is_sealed && ! is_creator`.
- Sentinel: `~/.master_ai_creator` (in $HOME, NOT in ~/scripts). When present, `is_creator` returns true.
- `pack_for_sale.sh` explicitly `rm -f "$OUTDIR/.master_ai_creator"` so a buyer bundle never carries the bypass. Buyers face the hard gate — that's the point of sealed mode.
- Banner copy: when creator + sealed, show `[CREATOR PASS — gate honors you, you may skip]` in green, not the red SEALED line.

**Do NOT:**
- Remove the creator bypass without Elijah's explicit permission.
- Add more "creator-only" features beyond dojo bypass without asking — this is a narrow carve-out, not a general role system.
- Re-run `pack_for_sale.sh` and worry that the creator file might leak — it lives in $HOME, outside the tar source. The explicit rm is belt-and-suspenders.

**Related:**
- `feedback_pack_it_up_for_sale.md` — the ship ritual. The creator marker is one of the ship-time exclusions.
- `feedback_give_it_a_home.md` — the creator bypass IS the home for "I built this" — it lives in the gate, not as a separate feature.

## Companion rule: "once you're in, you're in" (2026-04-19)

Elijah's words: *"It's only fair that once you pass the test one time to get into Sensei, that is unlocked for you. But you do have your projects and task pinned above it. Once you're in, you're in."*

- First successful gate entry writes `~/.dojo_entered` (ISO timestamp).
- On subsequent launches, `welcome_back()` in dojo_gate.sh runs BEFORE the full ritual:
  - Shows pinned project + task above a "WELCOME BACK" banner.
  - Offers c) continue · n) pick new · s) skip (if allowed) · x) back.
  - Only first-time users and "n) pick new" fall through to the full turn-in.
- Sealed-for-buyer users still face the full ritual on first entry; after that, welcome-back is available just like for anyone else.
- Creators have it implicitly via `is_creator` — they're treated as "entered before" even if the flag file doesn't exist.
- `pack_for_sale.sh` removes `.dojo_entered` from buyer bundles so buyers do the first-time ritual themselves.
