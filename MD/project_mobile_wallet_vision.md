---
name: Mobile / Voice-First / Digital-Wallet Vision
description: Elijah's 2026-04-20 late-night vision for Master AI on mobile devices — Sensei/Pupil as a voice-first digital wallet that auto-fills applications, reads iPhone updates aloud, captures ideas to Pupil by voice, controls music/speakers. A future v2 phase, not a v1 feature.
type: project
originSessionId: 01013ad7-2ca6-4c2c-a21f-0331a77353a0
---
Elijah's big-vision extension of Master AI, voiced 2026-04-20 late-night after the core loop (Plan/Review/Auto) landed on desktop.

## The concept

Master AI on tablets and phones, positioned as a **digital wallet that knows you**. User's long-form info (address, work history, references, preferences) lives in local memory. The AI uses it for:

- **Application auto-fill** — job apps (Indeed et al.), government forms, insurance. User fills once; AI extracts + emails completed applications on demand. ("They may not like that" — companies may resist AI-filled applications — but it's the user's own data going into their own application.)
- **Accessibility reader** — "Hey, what's in this iPhone update?" → AI reads the release notes aloud, user picks what to install without reading. Extends to news, long emails, terms of service, anything text-heavy.
- **Voice-first everything** — "play this song," "tell me about this," "log this idea to Pupil." Speaker-out instead of screen-read. Works while hands are busy, eyes are elsewhere.
- **Idea capture into Pupil by voice** — Elijah's exact example: *"Hey this is the idea I have. Can you log it into the pupil for me."* Pupil becomes the voice-dictated capture layer for thoughts.

## Technical feasibility (2026 state)

Code itself is tiny — the full Master AI Python + HTML + bash is ~200 KB. The weights are the weight:
- `qwen2.5:3b` ≈ 2 GB (fits on any modern phone)
- `qwen2.5:7b` ≈ 4 GB (fits on 8+ GB RAM phones)
- `llava` ≈ 4 GB (vision, optional)

**Platform paths:**
- iOS — Apple MLX framework (native ML), or llama.cpp iOS port, or Ollama via Termux-style sandbox
- Android — Termux runs Python + llama.cpp directly today; Ollama via shell
- Native apps could wrap the runtime with iOS/Android bindings

Mobile is not a rewrite — it's a port of the runtime with a touch-first UI layer over Pupil.

## Why this is Master AI, not a separate product

Every element listed above is already Master AI's DNA on desktop:
- Voice input (Sensei already handles speech-to-text via phone)
- Pupil as capture layer (already exists on browser)
- Local-first, user owns their data (founding principle)
- Auto-fill from memory (the memory store already exists)
- Tool execution + file writing (Sensei already does this)

The mobile vision is a form-factor extension, not a new product.

## How to apply (for future sessions)

- **Do NOT build this in v1.** Ship desktop Master AI first ($100 A+ target per `project_sale_readiness.md`). Mobile is a leveraged move once desktop has brand + users + stable code.
- **Don't hand this idea to a BigCo** (Apple, Anthropic, wallet company) — they'd absorb it, Elijah ends at zero. Memory `user_builder_vs_distributor.md` applies: he wins the builder game; the right move is to build it himself when the time comes.
- **If collaboration ever makes sense:** hardware-first AI device companies (Humane-style, Rabbit-style) or accessibility-focused nonprofits (Be My Eyes, Microsoft Accessibility) where incentive alignment works. NOT data-hungry platforms.
- **Positioning for later pitch:** *"Master AI for mobile is a voice-first digital wallet that runs on your own device. Your info stays yours. Fill out an application by speaking. Read your iPhone update aloud. Log ideas to your notes by voice. Nothing leaves the phone."*
- **Capture mechanism:** this memory file IS the idea log. Don't let it drift into Sensei's `.master_ai_memory` until v1 is shipping.

## Closing exchange that created this memory

Elijah asked: *"If you write this idea, who do you think I want to have it?"*

My answer: You don't want to hand it to anyone. It's the mobile phase of the product you're already building. File it, ship desktop, then build the phone port when desktop is on its feet. — captured 2026-04-20.
