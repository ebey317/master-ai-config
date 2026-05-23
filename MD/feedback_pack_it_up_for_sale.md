---
name: "Pack It Up" — two-phase permanence keyword (TESTING vs SALE)
description: Elijah's phrase has TWO distinct forms. "Pack it up" alone = prep for a testing round (freeze a snapshot, run the test checklist, do NOT seal for buyers). "Pack it up for sale" = final ship ritual (strip personal data, seal gate, tag version). Until either phrase is said, everything stays in open testing.
type: feedback
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---

## The two keywords — rule updated 2026-04-19

Elijah distinguishes between two freeze points:

### "pack it up" (alone) → PREP FOR TESTING
**What it means:** stop iterating, freeze what's on disk NOW, make sure everything is in a known runnable state, and hand it to a testing round. This is NOT a sale ritual — personal data stays, the creator bypass stays, the dojo gate stays soft, nothing gets stripped.

**What to do when this phrase fires:**
1. Verify `~/scripts/TEST_CHECKLIST.md` is up to date (reasoning loop live test, multi-user isolation, Tailscale phone access — the three acceptance tests)
2. Run static verify on any touched Python: `python3 -m py_compile ~/scripts/*.py`
3. Update `project_master_ai_state.md` — mark the current state as "sealed for test round YYYY-MM-DD"
4. Optional: git tag a `-pretest` snapshot (e.g. `v1.8-pretest-2026-04-19`) — restore point if a test breaks something
5. Do NOT remove personal data, do NOT seal the dojo gate, do NOT rename Sunkissed references, do NOT declare features permanent

The point is: testing is about RUNNING the product as Elijah, not HANDING IT to a buyer.

### "pack it up for sale" → PREP FOR SALE (the real ship)
**What it means:** everything in the "testing" freeze PLUS the buyer-bundle strip. This is the permanent tag. See the "PACK IT UP FOR SALE — UPDATED RITUAL" section below — all six steps fire.

### Near-variants
- "pack it" / "pack up" → interpret as the testing freeze. Ask to confirm before doing the sale-bundle strip.
- "seal it" / "ship to sale" → the SALE phrase. Full ritual.

### Until either phrase is said
Everything stays in open testing — no tags, no seals, no strips, no permanence markers. Features can be reverted at any time.

**Why:** Elijah treats Sensei (and the whole Master AI brand) as a product he's shaping toward resale. "Pack it up for sale" is his own mental checkpoint — it means *this specific iteration* is polished enough to hand to a buyer. Anything before that is raw dojo work.

**How to apply:**
- After implementing a feature, show it working → wait for "pack it up for sale" before tagging/committing as a new restore point
- If user says "revert" / "change it" before the phrase → it's still in-flight, revert freely, don't argue version history
- When the phrase IS said → do the full ship ritual: git add → commit with clear message → tag vN+1 → update project_master_ai_state.md → update MEMORY.md index if needed

## Dojo gate special case

The Sensei dojo gate (project+task picker at launch) is **soft during testing** — Elijah can skip through it, pick "Master AI" (which is always available), or enter without a task pinned. The gate shows the flow but does NOT hard-block.

**When "pack it up for sale" is said**, the gate flips to **hard mode** — no project+task, no dojo entry. This is the sale-ready behavior for a buyer.

**How to apply:** While testing, don't enforce the gate. Let Elijah in regardless. Only after the sale-keyword should the gate actually refuse entry. A simple flag file (e.g. `~/.dojo_gate_sealed`) or a check in the gate script against a memory/state marker is enough.

## Ship / Don't-Ship list (added 2026-04-19)

When packaging for sale, these rules are non-negotiable. Elijah doesn't repeat himself; encode them here:

### DOES NOT SHIP
- **Sunkissed Soul** — his flagship app is his own property; buyers never get access. Not the base44-hosted version, not any local port pointed at it, nothing.
- **SKS Hub Client** (`:5173` Vite app at `~/Downloads/sunkissed-soul`) — that's the internal test harness for Sunkissed. Rename / relabel / redirect as needed, but the "Sunkissed" and "SKS" names must not appear in buyer-facing UI, docs, or config.
- **Elijah's personal memory files** (`~/.master_ai_memory`, session logs, chat history) — wipe or exclude.
- **His API keys** (`~/.master_ai_keys`) — excluded, obviously.
- **His projects** in PROJECTS.md under "Project Boards" — ship empty boards (or a sample / starter project), not his actual in-flight work.

### SHIPS INSTEAD (what the buyer gets for "Remote")
- **Menu option 6 "Remote (connect to another node)"** — the UI and hookup instructions buyers need to point a second device at THEIR node. Not Sunkissed — just the remote-connect experience.
- Hookup info printout (host / LAN IP / Tailscale IP / all 4 port descriptions / copy-ready URLs / phone onboarding steps) — already wired via `launch_remote()` in master.sh.

### REQUIRED BUYER DOCUMENTATION

"Pack it up for sale" MUST include this paperwork — this is part of the $100 A+ promise, not optional:

1. **Extensive README** explaining:
   - What Master AI is (brand / system)
   - What Sensei is + how it works + how to talk to it (BEFORE they first open it)
   - What Pupil is + how it works
   - What Remote is + how to hook up a second device
   - Every menu option, one-by-one, with what it does and when to use it
   - Command reference (all Sensei commands — dojo, tasks open, done, mode, remember:, etc.)
   - The dojo gate philosophy (why you have to turn in a project to enter)
2. **Slideshow read-aloud alternative** — for buyers who don't want to read: a clickable slideshow that uses the TTS server (`:5050`) to read each slide aloud. Same content as the README, served interactively.
3. **Menu-option walkthrough** (bundled with README or in the slideshow) — explain each number so a stranger who opens `master.sh` for the first time is never confused.

**Why this matters:** Elijah's words — *"there's a lot of paperwork you can give, and if you don't want to do paperwork, you can do an extensive click-and-read-me slideshow that reads out loud."* He's not writing the docs himself — AI (Sensei or another instance) generates them as part of the ship ritual.

### PACK IT UP FOR SALE — UPDATED RITUAL

When Elijah says the phrase, perform these in order:

1. **Verify ship-readiness checklist** (from `project_sale_readiness.md`): multi-user wired, installer present, curriculum present, docs present, Sunkissed stripped
2. **Generate/update buyer docs** if missing:
   - `README_FOR_BUYER.md` (extensive, covers everything above)
   - `slideshow.html` (click-and-read, TTS-enabled)
3. **Strip personal data** from the package (memory, keys, sessions, personal projects)
4. **Seal the dojo gate** (`touch ~/.dojo_gate_sealed`) — if this is the buyer-bound build
5. **Relabel any SKS / Sunkissed references** in user-facing strings
6. `git add` → commit with clear message → tag `v<next>` → update `project_master_ai_state.md` → update `MEMORY.md` index

If any of steps 1–5 is missing, **say so and pause the ritual** — don't tag a half-packaged build as "for sale."
