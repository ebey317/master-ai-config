---
name: Walk the who-what-where-why-went-how cascade before proposing routing or handoff designs
description: When designing any routing, handoff, or decision-flow for Master AI (orchestrator, Plan→Review handoff, mode switching, etc.), walk the full 5W+H cascade for each concrete case BEFORE proposing code or structure. Don't jump to "local-first vs cloud-first" or "list of answerers" — that's skipping the cascade. The correct handoff falls out of the cascade; it's not picked ahead of it. Render to Elijah as numbered 1-6 with bold labels (**WHO** / **WHAT** / **WHERE** / **WHY** / **WENT** / **HOW**), then a single bold "Destination from the cascade: X." line, then a short paragraph naming which steps drove the pick (e.g. "from WHAT + WENT"), then 1-2 lines demoting the runners-up with the specific reason each lost. Format validated 2026-04-26.
type: feedback
originSessionId: 11fd1fb3-8c8a-4bf3-a76a-4a25a90390a4
---
Before proposing any routing / handoff / decision design, walk this cascade for each concrete case the user cares about:

1. **WHO** — who is sending the input (user on phone/tablet/monitor, voice-to-text vs typed, which UI — Sensei / Pupil / menu).
2. **WHAT** — what is the input actually asking for (chat? build/alter? reasoning? vision? current events? recall?).
3. **WHERE** — where does it go (which answerer / module / path) — this falls out of WHAT, not a blanket policy.
4. **WHY** — why that destination and not another (the reason must be about question-fit, not about default-ordering like "local first").
5. **WENT** — audit what actually happened in the code + harvest log. Where did it land? Did silent fallback fire? Did timeout trip?
6. **HOW** — the mechanism: specific file/line/function that implements the decision, and the mechanism that could go wrong.

**Why this is the rule:** Elijah 2026-04-21 — "that's why I want the who what where why went and how and then the cascade which you're not even doing that now so you're not really checking your memories." He also called out the prior turn where I proposed "route by question fit" as a flat reframe and immediately listed answerers (including dormant Scrappy) — that skipped the cascade and produced a shape-of-a-design without walking the actual cases. The messy state of his code is partly downstream of me jumping to structures instead of walking the cascade.

**How to apply:**
- For every routing / handoff / decision-flow proposal, pick 2–4 concrete cases (e.g. "hi", "build me a file", "what happened today", "read this image") and walk the cascade for each. The correct route for each case is the OUTPUT, not the INPUT.
- Only then propose code — and the code must follow the cascade that was walked.
- If the cascade surfaces a conflict with an existing memory (e.g. "Local Mode is Default"), name the conflict explicitly and ask before coding, don't silently override.
- WENT (step 5) is the audit step — it's where I have to open the harvest log / journalctl / code and verify reality, not recall memory. Memory is stale by design; WENT is fresh.
- Don't list dormant components in WHERE (see `feedback_no_dormant_in_designs.md` — no Scrappy, no chunker, no not-yet-built features in the live answerer set).

**Output format when presenting the cascade to Elijah** (validated 2026-04-26 — "thats how i wanna read that perfect"):
- Numbered list 1–6, one step per line, label in bold (**WHO** / **WHAT** / **WHERE** / **WHY** / **WENT** / **HOW**) followed by the case-specific content.
- After the six steps, a single bold line: **"Destination from the cascade: <pick>."**
- Then a short reasoning paragraph that names *which steps* the destination falls out of (e.g. "from WHAT + WENT") — don't restate the whole cascade.
- Then 1–2 lines demoting the runners-up with the specific reason each lost (e.g. "drops to second pass," "drops out for now"), not just "less good."
- Voice-typing-friendly: tight prose, no sub-bullets inside the steps unless a step genuinely has multiple beats.
