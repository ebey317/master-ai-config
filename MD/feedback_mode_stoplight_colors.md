---
name: Mode Colors Use Stoplight Semantics (2026-04-21 remap)
description: Sensei mode colors map stoplight meaning — Plan=RED #cc0000 (stop/no-exec, the approved hex from 04-20 tuning walk), Review=AMBER #c7761a (caution/per-step), Auto=GREEN #1a7a3a (go). "safe" mode is REMOVED — do not reintroduce.
type: feedback
originSessionId: c53f933b-955f-4b1d-8b2b-dc75f15b261b
---
**The rule (2026-04-21 remap — supersedes 04-20):**

| Mode   | Color       | Hex      | Meaning                                                    |
|--------|-------------|----------|------------------------------------------------------------|
| plan   | DEEP RED    | #cc0000  | STOP — drafts only, NO execution. Muted for long reads.    |
| review | AMBER       | #c7761a  | CAUTION — approve each RUN/CREATE/EDIT one at a time.      |
| auto   | GREEN       | #1a7a3a  | GO — runs without asking (destructive still pauses).       |

**Why this remap:**
Elijah 2026-04-21: *"plan should be this stop and think about it mode — I didn't formulate the whole plan. Review is yellow where we go through. We press OK or accept for every review every decision and approve it and it runs. Auto is Auto."*
Plan is literal full-stop → red. Review is proceed-with-caution-one-step-at-a-time → amber. Auto is go → green. The old "Review gets red because it's stricter than Plan" framing was dropped — what matters is whether the mode EXECUTES, not how strict the gating feels.

**Plan's red is #cc0000 — the approved hex:**
Elijah 2026-04-21: *"go to the red I approved."* That's the `#cc0000` "true red without glare or tint" he locked 2026-04-20 after a full tuning walk. Under the 04-21 remap it now lives on Plan (moved from Review). He called it "dark red" and "not super red" — it satisfies both "don't blare" and "clearly red." **Do NOT re-tune this hex without him explicitly asking.** Review's amber (#c7761a) and Auto's green (#1a7a3a) are unchanged — they already worked.

**"safe" mode is GONE — 2026-04-21:**
Elijah: *"yes change everything and get rid of safe."* The legacy alias (`mode safe` → `mode review`) has been removed from master_ai.py. Every doc reference to `mode safe` has been rewritten. If a future session sees `mode safe` anywhere, it's drift and needs correction. The three modes are **plan / review / auto** — only.

**Belt-color rule (2026-04-20, still applies):** Only adult-legit karate belts — {white, yellow, green, blue, purple, brown, red, black}. No orange / rust / pumpkin hexes. Elijah: *"only kids get an orange belt in martial arts."* The amber slot (#c7761a) passes because it reads as yellow-belt with warmth — don't push it further orange.

**Red hex history (for reference, do not re-tune without explicit ask):**
`#cc0000` was locked 2026-04-20 after a long tuning walk: `#8b1a1a → #b91c1c → #ef4444 → #dc2626 → #ef4444 → #dc143c → #ef4444 → #c0392b → #ef4444 → #cc0000`. Elijah called it "true red without glare or tint" — the final resting point. Under the 04-21 remap, the SAME hex moved from Review to Plan. Don't re-walk this chain. If a future question reopens it, point at this memory.

**How to apply:**

- Code source of truth: `sensei_tui.py:_MODE_ACCENT` dict (~line 360). Plan=#cc0000, Review=#c7761a, Auto=#1a7a3a.
- All doc references (`howwework.txt`, `README_FOR_BUYER.md`, `tailscale_remote.html`, `slideshow.html`, `benchmark_sensei.sh`) have been updated 2026-04-21.
- Pupil's belt-color theme picker (`pupil.html` `theme-dot-*`) is a **separate concept** — user-chosen background theme, unrelated to mode semantics. Do not touch.
- Never re-invert this without Elijah explicitly reversing it. The intuitive stop/go signal is load-bearing.
- If you see `safe=` anywhere as a Sensei mode, it's a regression — fix it.
