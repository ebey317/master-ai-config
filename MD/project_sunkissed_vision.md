---
name: Sunkissed Soul Vision & System Taxonomy
description: Origin story of Elijah's stack — base44.com flagship, local Master AI brand, off-grid North Star, authoritative project taxonomy
type: project
originSessionId: 54ca49b7-de9e-493b-a740-da74b35159d4
---
## Origin

**Sunkissed Soul** is Elijah's flagship app — spiritual / music / study content — and it runs at **base44.com** (a paid low-code platform). The real Sunkissed Soul app lives there and he is currently paying for it.

The whole local stack on Madam-Mary started when Elijah built a local "hub" to connect Sunkissed-on-base44 to his own local Ollama. In the process he discovered **he could self-host the same pattern** on his own PC — cut the subscription, and turn base44's polish into his own free-forever local system. That discovery is the origin of Master AI, Sensei, and every other build on the machine.

## Correct taxonomy (memorize this)

- **Master AI** = the **BRAND / whole system** — umbrella for the menu + every UI it launches.
- **Sensei** = **one UI inside Master AI** — the tmux terminal AI agent (`~/scripts/master_ai.py`, menu option 4). Renamed from "PC Control / PC Agent" so two confusable terminal UIs could be told apart. Calling *this UI* "Master AI" was the multi-week source of confusion — it's fixed now.
- **Other Master AI sub-UIs**: the menu itself (`master.sh`), the Web Chat (`:8080`), the TTS server (`:5050`).
- **`:5173` Vite app** at `~/Downloads/sunkissed-soul` is the **SKS Hub Client (local)** — a test harness for wiring the local hub into the real base44 Sunkissed Soul. It is **NOT** Sunkissed Soul. Do not call it that.

**Why:** Elijah corrected me on this directly in session 2026-04-18. Getting the names right is load-bearing — he was confused for weeks by overlapping UI names.

**How to apply:** Never refer to `:5173` as "Sunkissed Soul" in conversation, file names, or memory. The flagship Sunkissed Soul is external (base44.com) and gets its own entry. When he says "my app" he means the base44-hosted one; when he says "the hub" or "the test on 5173" he means the local Vite harness.

## North Star

An **off-grid, fully self-sufficient AI system**: local model training on his own directories, multi-port local apps talking to each other in a Tailscale-style mesh (but local), eventually **replacing base44 entirely** and hosting Sunkissed Soul on his own hardware.

Everything he builds should be read through this lens — features that lock him into external paid services fight the North Star; features that make the local stack more capable move toward it.

## The full flagship arc (clarified 2026-04-18 late session)

Sunkissed Soul is the **flagship experience** — everything else in the Master AI stack orbits it and pours into it. The full vision:

- **Sunkissed Soul** ← flagship UI; the "Soul" is the user's personal AI companion that travels with them
- **Sunkissed AI** (programming/backend) ← knows everything about the Sunkissed app; tied directly into Master AI so the umbrella intelligence is app-aware
- **Vision / scanner inputs** (all offline, all on Madam-Mary):
  - **Scrap scanner** — camera looks at a house of trash/waste, identifies raw materials, outputs "you can build X from this" + step-by-step blueprint
  - **Apothecary scanner** — camera looks at plants/raw botanicals, outputs "you can make this medicine/remedy from this"
- **Off-Grid Civilization Information** is the knowledge spine behind both scanners — the rebuild-from-scratch corpus
- **Master AI** is the orchestrator/pointer that routes all of it — app state, vision, knowledge, user soul
- **All UIs connect remotely** (phone, desktop, future mesh nodes) — one user, many surfaces, one AI

This is why Elijah "didn't get rid of the vision" even when the scanner idea sat idle for a while — it was waiting for Master AI to exist as the brain that could actually use it.

**How to apply:** When Elijah talks about Sunkissed Soul, do NOT treat it as just a music/spiritual content app. It's the front door to an off-grid rebuild-from-scratch AI system. Any feature suggestions should consider how they feed the flagship arc (scanner → blueprint → apothecary → soul companion). Connect the dots — don't silo the projects.

## Idea capture

The authoritative project list is `~/scripts/PROJECTS.md` (seeded in this same session). Menu option 9 ("Log a new idea / POC") now prompts for title + description + status and appends a structured entry to that file. Before this fix, option 9 dumped one-line ideas into `master.log` and only one entry existed since Apr 7 (content: literally `"2"`).

"One vague idea turns into all this" — future ideas should be captured, not lost to memory drift.
