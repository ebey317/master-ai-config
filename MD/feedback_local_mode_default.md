---
name: Local Mode is the product stance — routing is route-by-fit
description: Elijah's product stance (2026-04-19, clarified 2026-04-21 pm). Master AI is LOCAL-FIRST as a resilience guarantee: the machine stays capable when the cloud is gone. For per-turn routing, 2026-04-21 reframe is "route by fit" — plain chat with a key present can go to Groq directly. `local:` prefix always forces local.
type: feedback
originSessionId: 6dd5863d-4ed5-45b6-ba4a-581fbc1b9fc0
---
## 2026-04-21 update — two separate questions

This memory was originally written as one rule covering two different things. Splitting them:

1. **Product stance (UNCHANGED):** Master AI is built to work without the cloud. *"If I fell down the hole for six months... I need this to still work."* Continuity is the product. `feedback_give_it_a_home.md` + `project_materializing_whitespace.md` + `project_sunkissed_vision.md` all lean on this.

2. **Per-turn routing (CHANGED 2026-04-21 pm):** The orchestrator no longer forces local-first on every turn. Elijah's reframe: *"doesn't necessarily mean local has to be 1st."* Plain chat (no CODE/ALTER/COMPLEX/REASONING words) routes to Groq when a key exists — ~1s reply vs 1–5 min cold-start on the i7-6700T. See `project_pending_routing_bug.md` (RESOLVED) for the content-routed chat lane at master_ai.py:996-1004. `local:` prefix still forces local.

## The rule (as it stands 2026-04-22)

Cloud routing happens when:
- User prefixes a message with `fast:` / `deep:` / `vision:` (explicit opt-in), OR
- User is in Connected Mode (`mode connected`/`cloud`/`online`), OR
- **(NEW)** The turn classifies as plain chat AND a Groq key exists — content-routed chat lane at master_ai.py:996-1004.

Local is forced when:
- User prefixes with `local:` / `private:` (explicit opt-in).
- Turn contains CODE/ALTER/COMPLEX/REASONING signal.
- Build/edit work (CREATE/EDIT directives) — always goes through master-ai first.

**Why (Elijah's words, 2026-04-19):**
- *"I wanna be local dependent. I just wanna have the benefits of the cloud... know that it degrees just down. My machine is still capable of producing on this level. A machine I keep for 10 to 20 years."*
- *"If the world ended or if I fell down the hole for six months falling, and then I landed and climbed back up, it took me two years to climb — I need this to still work."*
- Real-world tools don't need to keep up with the digital world. A 3.5" floppy still boots. A cassette still plays. The product is a tool, not a subscription.

**Vocabulary rules:**
- **User-facing:** Local Mode · Connected Mode · In-House Mode · Offline Companion — plain, non-alarming.
- **Internal code / file values:** `apocalypse` / `peacetime` stays in `~/.master_ai_run_mode`, variable names, Sensei behavior contract. That vibe is the dev-side soul of the product; don't strip it. Just don't show it to customers.
- **NEVER** say "apocalypse mode" in slideshow.html, README_FOR_BUYER.md, Pupil UI, or any buyer-facing copy. Those use "Local Mode."

**How to apply:**
- If asked to flip Sensei to cloud-first by default, push back. That's the wrong frame. Make it a mode toggle with both halves explained (gain + trade) in plain language.
- Connected Mode's trade-offs include: needs internet, cloud may log prompts, free-tier quotas can hit. These get stated honestly.
- Local Mode's trade-offs include: human-pace replies (not instant), won't chase newest cloud models. These get framed as a feature, not a loss — the product's value is continuity, not bleeding-edge speed.
- `fast:` / `deep:` / `local:` / `private:` explicit prefixes always win, independent of mode.

**Related memory:**
- `project_apocalypse_mode.md` — the original two-mode framing. Now wired.
- `project_materializing_whitespace.md` — "when the AI fades, the scaffolding stays" aligns with Local Mode as product stance.
- `project_sunkissed_vision.md` — off-grid corpus is a Local Mode use case.
