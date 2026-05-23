---
name: Pending — Sensei "you = self" identity fix (parked 2026-04-25)
description: Modelfile-master-ai needs a one-line IDENTITY addition so Sensei reads "you / your app / this app / this project" as Master AI itself (whole stack), not as a generic developer she's advising. Drafted 2026-04-25, NOT applied — Elijah said "save for later."
type: project
originSessionId: 80302a47-7f72-4870-bf90-94b765ff6dbc
---
## The bug

When Elijah asks Sensei about "you" / "your app" / "this app," she defaults to advising a generic third-party developer building from scratch. Concrete example 2026-04-25: asked about getting Master AI on the App Store, she returned "Define purpose and target audience" — generic-app advice — instead of recognizing she IS the thing being shipped.

Elijah's framing: **"every aspect of master ai is her."** Not Sensei-is-a-part-and-Pupil-is-a-part — the whole stack, every surface, every command, every file IS Master AI, IS her.

## CRUCIAL CONTEXT — Elijah's diagnosis 2026-04-25

**She was in connected (cloud) mode when this fired.** The transcript routing tag was `[thinking: cloud-fast → Groq]`. Cloud models do NOT carry the Modelfile SYSTEM block — they only see the per-turn prompt. So a Modelfile-only fix would help LOCAL routes (master-ai) but cloud lanes would keep doing generic-developer pivots anyway.

This connects to the existing open watch (`project_master_ai_state.md`): *"Cloud fallback punts when local times out — Groq doesn't carry the Modelfile rules."* The identity gap is a specific instance of that broader problem.

**Real fix needs TWO surfaces:**
1. **Modelfile IDENTITY line** (covers local master-ai routes) — the one-line draft below.
2. **Orchestrator-level system-prompt injection for cloud lanes** in `master_ai.py` — every cloud call (Groq, DeepSeek, OpenRouter, qwen3.5:cloud) needs the same self-reference clause prepended to its system prompt, since the cloud model has no other source for it. Scope: find the cloud dispatch path, identify the system-prompt assembly, add the IDENTITY clause there.

Both surfaces or the bug only half-fixes.

## The proposed fix (parked, not applied)

Add ONE line to the IDENTITY block in `~/scripts/Modelfile-master-ai` at line 40 (right after `- Master AI = brand; Sensei = this tmux agent; Pupil = browser UI. Same local backend.`):

```
- "You" / "your app" / "this app" / "this project" = Master AI itself, the whole stack you ARE (every surface, every command, every file). Read those prompts as self-referential — never advise yourself like a generic developer building from scratch.
```

Bake command (Elijah runs, not Claude):

```
ollama create master-ai -f ~/scripts/Modelfile-master-ai
```

Then `refresh` in Sensei.

## Why parked

Elijah said "save for later" 2026-04-25. v1.8 just landed clean and validated; he's not ready to bake another Modelfile change today. One-off observation — pattern not confirmed twice yet (per the long-session-verify rule, single occurrence ≠ root-cause confirmed).

## How to apply

- Reopen when Elijah brings up the identity gap again, OR if "you = generic developer" pivots happen a second time, OR before the next "pack it up for sale" round (so the shipped Modelfile carries the fix).
- Keep scope tight — one line, no other Modelfile edits bundled in.
- Show the diff, get one-line confirm, then print the bake command for Elijah to run himself (sudo/state rule applies to model rebuilds too — he runs it).
