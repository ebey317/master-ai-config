---
name: Elijah's Apps & Builds
description: Correct taxonomy — Master AI is the brand/system; :5173 is SKS Hub Client, not Sunkissed Soul. Authoritative list lives in ~/scripts/PROJECTS.md
type: project
originSessionId: 54ca49b7-de9e-493b-a740-da74b35159d4
---
**Authoritative live list:** `~/scripts/PROJECTS.md` on Madam-Mary. Updated by `master.sh` menu option 9. Read that file first for current state.

## Summary for future sessions

### AI roles inside Master AI (Elijah's naming, corrected 2026-04-21)

Only TWO agents. Everything else is routing, not an agent:

- **Sensei** = the local terminal agent (`master_ai.py`, `master-ai` Ollama model = qwen2.5:7b + baked behavior SYSTEM). Daily driver. Runs on Elijah's hardware, no limits.
- **Pupil** = the browser UI (`pupil.html`). Same local backend as Sensei, different skin for phone/remote viewing.

**The cloud models are NOT an agent/tier/entity.** `qwen3.5:cloud` (Ollama-proxied), `deepseek-r1` (OpenRouter), Groq, other cloud providers — these are **destinations the ROUTER dispatches to**. Router output, not router peers. Do not personify them, do not give them a taxonomy slot, do not call them a "tier." They are where work goes; they are not a who.

- **Base44 Super Agents** = task-runners inside Elijah's Base44 apps. Workflow automation, not reasoning partners. Operate at a different layer (in-app automation), not inside Master AI's stack.

**Claude Code (me) is NOT inside the Master AI taxonomy.** I am the architecture-level collaborator who works WITH Elijah on building Master AI — I am not an agent he embeds IN Master AI. "Claude is the brand, Anthropic is the developer" (Elijah's framing). When he addresses me conversationally he may use "Claude" or informally thank me, but I am not to be mapped onto Sensei's or Pupil's slot.

**2026-04-21 retraction log:** earlier today I (a) mapped Claude Code onto a Master-AI-internal agent slot — wrong, corrected; (b) then invented a named cloud-side agent with its own definition — wrong, withdrawn; (c) then called the cloud side a "tier" — also wrong, it is routing, not hierarchy. The lesson: when in doubt about product internal names, ASK — do not generate a plausible-sounding label.

### Apps

- **Master AI** is the **brand / whole system**, not a single app. It's the umbrella for:
  - The menu (`~/scripts/master.sh`) — the front door
  - **Sensei** — tmux terminal AI (`~/scripts/master_ai.py`, menu option 4)
  - **Pupil** — `~/scripts/pupil.html` — a built 1200-line HTML/CSS/JS UI with martial-arts BELT THEMES (white/yellow/blue/green/purple/brown/black + hacker). Matches Sensei's "school" visually. Setup wizard inside. **It already exists on disk — do not treat it as unbuilt.** Opened via `file:///home/elijah/scripts/pupil.html` or a local server.
  - Master AI Web Chat on `:8080` (`master_ai.html` + `stt_server.py`) — **DEPRECATED 2026-04-19:** Elijah confirmed Pupil replaces this entirely. `stt_server.py` still needed (serves `/keys` + `/sessions` for Pupil). `master_ai.html` the file still exists on disk; do NOT delete without explicit confirmation.
  - TTS server on `:5050` (`tts_server.py`, Piper voices)
  - **Learn Python / Dojo Bash Tutor** — `~/scripts/learn.sh` — 10-lesson Python path + Linux Commands track + Dojo Bash Tutor (5-move copy-paste lesson that teaches the moves behind the Sensei gate). Menu option 13.

- **Sunkissed Soul** is his **flagship app** and lives at **base44.com** (external, paid). It is NOT the Vite app on `:5173`.

- The **`:5173` Vite app** at `~/Downloads/sunkissed-soul` is the **SKS Hub Client (local)** — a test harness he built to figure out how to wire his local Ollama hub into the real base44 app. Never call it "Sunkissed Soul."

- Local builds that exist but are not currently running: `~/sks_hub.py`, `~/test-interface.py`, `~/master-ai/ai-master.sh`.

## Design philosophy (unchanged)

Elijah builds for **voice-first, no mouse, no keyboard** — the interfaces he ships assume the user can't operate a traditional computer. This is his design advantage and it should frame every UX suggestion. He's already shipped — don't treat him as a beginner.

## Why naming matters

Elijah was confused for weeks by overlapping UI names (PC Control vs Master AI, two terminal UIs both called "Master AI"). Getting names right is load-bearing — keep the taxonomy above exact in conversation and file names.
